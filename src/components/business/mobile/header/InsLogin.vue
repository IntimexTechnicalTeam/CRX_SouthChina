<template>
<div>
    <!-- 正常购买模式 -->
    <div class="memberlogin">
        <div class="menberCentral" @click="menberCentral">
          <div class="Menbericon">
            <img class="showMenberCentral" src="/images/mobile/icon_22.png">
            <div v-if="isLogin">
              {{user}}
            </div>
            <div v-else>
              {{$t('Login.LoginTitle')}}
            </div>
          </div>
            <div class="lang_flow" v-show="showMenberCentral" @click="memberCentral">
                <div data-to="/account/memberInfo" class="ii">{{$t('Account.MemberInformation')}}</div>
                <div data-to="/account/modifyPassword" class="ii">{{$t('MemberInfo.ModifyPassword')}}</div>
                <!-- <div data-to="/account/notification" class="ii">{{$t('Account.MyMessages')}}</div> -->
                <!-- <div data-to="/account/message" class="ii">{{$t('Account.LatestNews')}}</div> -->
                <div data-to="/account/ptxorder" class="ii">{{$t('Enquiry.Servicestatus')}}</div>
                <div @click="logout">{{$t('Account.Logout')}}</div>
            </div>
          </div>
    </div>
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Cookie from 'js-cookie';
import { Message } from 'element-ui';
import sdk from '@/sdk/InstoreSdk';
@Component
export default class InsLangSwitch extends Vue {
  private showMenberCentral:boolean = false;

  get user () {
    return this.$store.state.user;
  }

  get isLogin () {
    return this.$store.state.isLogin;
  }
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  menberCentral () {
    if (!this.$Storage.get('isLogin')) {
      window.location.href = '/account/login';
    } else {
      this.showMenberCentral = !this.showMenberCentral;
    }
  }
  memberCentral (e) {
    let target = e.target as HTMLElement;
    if (target.className === 'ii' && target.dataset.to) {
      this.$router.push({
        path: target.dataset.to
      });
    }
  }
  logout () {
    this.$Api.member.logout().then((result) => {
      if (result) this.$message('Message.SucceedInOperating');
      this.$router.push('/');
      this.Reload();
    });
  }
  getAccount () {
     let _this = this;

     sdk.api.member.getAccount().then(
       function (data) {
         if (data.Logined) {
           _this.$store.dispatch('setUser', (data.FirstName + ' ' + data.LastName).toUpperCase());
           _this.$store.dispatch('doLogin');
         } else {
           _this.$store.dispatch('Logout', true);
         }
       },
       function (data) {
         Message({
           message: data,
           type: 'error',
           customClass: 'messageboxNoraml'
         });
       }
     );
   }
  mounted() {
    console.log(this.user, 'user');
    this.getAccount();
  }
}
</script>
<style scoped lang="less">
  .menberCentral{
    float: left;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-left: 1rem;
    .Menbericon{
      display: flex;
    align-items: center;
    justify-content: center;
    >div{
      font-size: 1.4rem;
    }
    }
    .lang_flow{
    position: absolute;
    top: 3.8rem;
    left: 0px;
    width: 100%;
    background: #FFF;
    z-index: 999;
    >div{
      color:#000;
      font-size: 1.4rem;
      height: 4rem;
      display: flex;
      justify-content: center;
      align-items: center;
      border-bottom: 1px solid #ccc;
    }
    }
    img{
      width:1.5rem;
      margin: 0 auto;
      display: block;
      margin-right: 1rem;
    }
  }
</style>
