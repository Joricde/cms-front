<template>
  <div>
    <Card class="card_size" dis-hover title="客户信息">
      <Form :label-width="120">
        <FormItem label="姓名">
          {{ userFormItem.name }}
          <Button style="margin-left: 5%" type="primary"
                  @click="sendMail(userFormItem.accountId)">
            <Icon type="ios-mail" size=24 />
          </Button>
        </FormItem>
        <FormItem label="联系方式">
          {{ userFormItem.telephone }}
        </FormItem>
        <FormItem label="到达时间">
          {{ userFormItem.arriveTime }}
        </FormItem>
        <FormItem label="到达地点">
          {{ userFormItem.arriveSite }}
        </FormItem>
      </Form>
    </Card>
    <Card class="card_size" dis-hover title="订单信息">
      <Form :label-width="120">

        <FormItem label="预约时间">
          <DatePicker type="datetime" disabled="" v-model="orderInfoFormItem.Appointment"
                      format="yyyy-MM-dd: HH:mm"></DatePicker>
        </FormItem>
        <FormItem label="接车时间">
          <DatePicker type="datetime" v-model="orderInfoFormItem.pickupTime" v-bind:disabled="userFormItem.driverCheck"
                      format="yyyy-MM-dd: HH:mm"></DatePicker>
        </FormItem>
        <FormItem label="接车地点">
          <Input v-model="orderInfoFormItem.pickupSite" v-bind:disabled="userFormItem.driverCheck"></Input>
        </FormItem>
        <FormItem label="车牌号">
          <Input v-model="orderInfoFormItem.carNumber" v-bind:disabled="userFormItem.driverCheck"></Input>
        </FormItem>
        <FormItem>
          <Button type="primary" style="margin-right: 10%" v-bind:disabled="userFormItem.driverCheck"
                  @click="confirmOrder">确认订单
          </Button>
        </FormItem>
      </Form>
    </Card>
    <Modal
        style="padding: 20px"
        footer-hide
        v-model="openWriteMail">
      <div style="padding: 5%">
        <WriteMail ref="fillAccount" :send-id="this.sendMailAccount" @closeSendModal="closeSendModal"></WriteMail>
      </div>
    </Modal>
  </div>
</template>

<script>
import WriteMail from "@/components/common/WriteMail";

export default {
  name: "Detail",
  components: {WriteMail},
  data() {
    return {
      id: this.$route.params.id,

      userFormItem: {
        accountId:this.$route.query.data.accountId,
        name: this.$route.query.data.name,
        conferenceId: this.$route.query.data.conferenceId,
        userId: this.$route.query.data.userId,
        gender: '',
        telephone: this.$route.query.data.telephone,
        arriveTime: new Date(this.$route.query.data.arriveTime),
        arriveSite: this.$route.query.data.arriveSite,
        reserveTime: this.$route.query.data.reserveTime,
        driverCheck: this.$route.query.data.driverCheck,
        driverId: '',
        pickupTime: '',
        pickupSite: '',
        carNumber: '',
        userCheck: '',
      },
      orderInfoFormItem: {
        conferenceId: this.$route.query.data.conferenceId,
        userId: this.$route.query.data.userId,
        Appointment: this.$route.query.data.reserveTime,
        pickupTime: this.$route.query.data.pickupTime,
        pickupSite: this.$route.query.data.pickupSite,
        carNumber: this.$route.query.data.carNumber,
      },
      openWriteMail: false,
      sendMailAccount: {account: ''},
    }
  },
  mounted() {
    this.test()
  },
  methods: {
    test() {
    },
    confirmOrder() {
      let that = this
      let data = {
        conferenceId: this.orderInfoFormItem.conferenceId,
        userId: this.orderInfoFormItem.userId,
        pickupTime: this.orderInfoFormItem.pickupTime.getTime(),
        pickupSite: this.orderInfoFormItem.pickupSite,
        carNumber: this.orderInfoFormItem.carNumber,
      }
      this.$axios({
        method: 'post',
        url: `${this.$baseURI}/api/driver/reservation/check`,
        data: data
      }).then(function (response) {
        if (response['data']['result']===true){
          that.$Message.success("确认订单成功");
          that.push('/driver/reservation')
        }else {
          that.$Message.error(response['data']['message']);
        }
      })
    },

    sendMail(accountId) {
      this.sendMailAccount.account = accountId;
      console.log(this.sendMailAccount.account)
      this.$refs.fillAccount.autoFillAccount()
      this.openWriteMail = true
    },
    closeSendModal(fromChild) {
      this.openWriteMail = fromChild
    },
  },
}
</script>

<style scoped>
.card_size {
  width: 600px;
}

@media screen and (max-width: 800px) {
  .card_size {
    width: 100%;
  }
}
</style>