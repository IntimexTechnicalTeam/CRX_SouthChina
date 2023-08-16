<template>
 <div class="accountHeader" :class="{'ENG' : $Storage.get('locale') === 'E'}">
   <div class="accountBg ">
    <div class="NormalTitle">
      <p class="memberCenterTitle center">
        {{$t('Account.MemberCenter')}}
      </p>
    </div>
    <!-- <p class="accountUser">{{MemberName}}</p> -->
    <div class="accountMeun">
        <p><router-link to="/account/memberInfo">{{$t('Account.MemberInformation')}}</router-link></p>
        <p><router-link to="/account/modifyPassword">{{$t('MemberInfo.ModifyPassword')}}</router-link></p>
        <!-- <p><router-link to="/account/notification">{{$t('Account.MyMessages')}}</router-link></p> -->
        <!-- <p><router-link to="/account/message">{{$t('Account.LatestNews')}}</router-link></p> -->
        <p><router-link to="/account/ptxorder">{{$t('Enquiry.Servicestatus')}}</router-link></p>
    </div>
   </div>
 </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
import sdk from '@/sdk/InstoreSdk';
@Component
export default class accountHeader extends Vue {
   MemberName:string='';
   TotalPoints:string='';
   // 获取会员信息
   getMemberInfo () {
     let _this = this;
     sdk.api.member.getProfile().then(
       function (data) {
         console.log(data, 'datadata');
         _this.MemberName = data.FirstName + '  ' + data.LastName;
       }
     );
   }
   // 获取会员总积分
   getMemberTotal () {
     this.$Api.pointsApi.GetMemberTotalPointsInfo().then((result) => {
       this.TotalPoints = result.data.TotalPoints;
     });
   }
   get currentlang () {
     return this.$Storage.get('locale');
   }
   mounted () {
     this.getMemberInfo();
     this.getMemberTotal();
   }
}
</script>

<style scoped lang="less">
/*頁面中間目錄*/
ul,li{
    list-style: none;
}
.clear{
    clear: both;
}
.Emeun {
  p{
    img{
      width: 100%;
    }
  }
}
.accountHeader{
  width: 100%;
  display:inline-block;
  .memberCenterTitle{
    font-size: 2rem;
    text-align: center;
    display: block;
    padding-top: 2rem;
    color:#333;
    text-transform:uppercase;
  }
  .accountBg{
    width:100%;
    display: inline-block;
    box-sizing: border-box;
    padding-bottom: 1rem;
    // background: url('/images/pc/mobilebg.png') no-repeat center center;
    background-size: cover;
    .accountMeun{
      width: 90%;
      margin: 0 auto;
      border-bottom: 3px solid #ccc;
      display: flex;
      justify-content: space-between;
      p{
        display: block;
    /* height: 3.5rem; */
    margin: 1rem auto;
    margin-bottom: 1.5rem;
        a{
          // background: #fff;
          display: flex;
          width: 100%;
          align-items: center;
          justify-content: center;
          color:#333;
          border-radius: 20px;
          font-size: 1.4rem;
          height: 100%;
          text-transform:uppercase;
          font-weight: bold;
        }
        .router-link-active {
          // background: @base_color!important;
          color: #3c408e!important;
          position: relative;
          &::before{
            content: '';
            width: 0;
            height: 0;
            border-top: 12px solid transparent;
            border-left: 12px solid transparent;
            border-right: 12px solid transparent;
            border-bottom: 12px solid #3c408e;
            position: absolute;
            bottom: -14px;
            left: 50%;
            transform: translateX(-50%);
          }
        }
      }
    }
    .accountUser{
      font-size: 2rem;
      text-align: center;
      color:#333;
      padding-top: 2rem;
      padding-bottom: 2rem;
    }
    .accountTop{
      width: 70%;
      margin: 0 auto;
      img{
        width: 100%;
      }
    }
  }
  .NormalTitle{
    justify-content: left;
    margin-bottom: 3rem;
    width: auto;
    // height: 3rem;
    text-align: left;
    padding-top: 0;
    padding-left: 2rem;
    .center{
      position: relative;
      color: #333333;
      font-family: 'SourceHanSerifCN-Bold' !important;
      background-image: none;
      height: auto;
      padding-left: 1.5rem;
      font-size: 2rem;
      line-height: 3rem;
      width: auto;
      padding-top: 0;
      text-align: left;
      &::before{
        content: '';
        width: 1rem;
        height: 1rem;
        background-color: #3c408e;
        position: absolute;
        left: 0;
        bottom: -18px;
        transform: rotateZ(45deg);
      }
      &::after{
        content: '';
        width: 12rem;
        height: 2px;
        background-color: #3c408e;
        position: absolute;
        bottom: -13px;
        left: 1rem;
        transform:translateX(0);
      }
    }
  }
}
.ENG.accountHeader {
  .accountBg .accountMeun p a{
        text-transform: unset;
        text-align: center;
  }
  .NormalTitle .center::after{
    width: 22rem;
  }
}
</style>
