<template>
  <div>
    <v-topbarback text="30日内到期本金"></v-topbarback>
    <div class="content">
      <scroller style="top: 0px"
                :on-refresh="refresh"
                :on-infinite="infinite">
      <div class="bg-fff" v-for="item in list">
        <div class="mlrt-15 pdt-15 text-left">
          <p><img src="../assets/warn_borrower.svg" class="pi-img2 mr5">
            <span class="mr5 c-3d fz-16">借款人：{{item.name | len}}</span>
              <a :href="tel+item.tel"><img src="../assets/query_tel.svg" class="pi-img2 mr5"></a>
            <a :href="sms+item.tel+sms1+item.name+sms2+item.depAlias+sms3+item.should_repayDate+sms4+item.should_repayPrincipal+sms5"><img src="../assets/query_message.svg" class="pi-img2 mr5"></a>
            <span class="text-categray c-blue">本金</span>
          </p>
          <div class="text-info c-80">客户经理：{{item.afterLoanManageIdName}}</div>
          <div class="text-info c-80">渠道：{{item.channelName}}</div>
          <div class="clearfix"></div>
          <div class="fill-line"></div>
          <div class="text-info c-80">应还日期：{{item.should_repayDate}}</div>
          <div class="text-money">应还金额：{{item.should_repayPrincipal}}元</div>
          <div class="clearfix"></div>
        </div>
      </div>
      </scroller>
    </div>
  </div>
</template>

<script>
  import topbarback from '@/components/topbarback/topbarback'
  import config from '@/config/config'
  import check from '@/util/check'
  import getuser from '@/util/getuser'
  import Vue from 'vue'
  import VueScroller from 'vue-scroller'
  Vue.use(VueScroller)
  export default {
    name : 'warning_thirty',
    components : {
      'v-topbarback' : topbarback
    },
    data(){
      return {
        user: getuser(),
        pageNum: 1,
        pageSize: 6,
        list:[],
        dataList:[],
        tel:'tel:',
        sms:'sms:',
        sms1:'?body=尊敬的【',
        sms2:'】:您在【',
        sms3:'】:的借款应在【',
        sms4:'】还款【',
        sms5:'】元,请筹备资金按时足额还款，否则影响您的信用记录，谢谢合作！'
      };
    },
    mounted(){
      this.getWarnThirtyList(1)
    },
    methods: {
      refresh (done) {
        this.list.length=0;
        this.getWarnThirtyList(1);
        done()
      },
      infinite(done) {
        this.pageNum++;
        setTimeout(() => {
          if (this.dataList.length <1) {
            done(true)
            return;
          }else{
            this.getWarnThirtyList(this.pageNum)
            done()
          }

        }, 500)
      },
      getWarnThirtyList(page){
          this.$http.post(config.baseUrl+'/app/monthprincipal.do', {
              'userId': this.user.id,
              'specPriv':this.user.specPriv,
              'pageNum':page,
              'pageSize':this.pageSize
          }, {
              headers: {
              },
              emulateJSON: true
          }).then(function(response) {
              if(response.data.code ==1001){
                  this.$router.replace({ path: '/login/go' })
              }
              this.dataList=response.data.data.list
              this.list=this.list.concat(this.dataList)
              if(this.list.length<=0){
                  layer.open({
                      content:'暂无数据',
                      btn:['知道了'],

                  })
//                  this.$router.go(-1);
              }

          }, function(err) {
              this.$router.replace({ path: '/login/go' })
          });

      }
    },
      filters : {
          len(value) {
              if(value.length>6){
                  return value.substring(0,6);
              }else{
                  return value;
              }
          }
      }
  }
</script>


<style scoped>
</style>
