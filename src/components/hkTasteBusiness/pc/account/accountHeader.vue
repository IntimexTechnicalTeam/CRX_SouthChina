<template>
  <div class="accountHeader" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="memberBg">
      <div class="innerBox">
        <p class="memberCenterTitle">
          <span class="center">{{ $t("Account.MemberCenter") }}</span>
        </p>
        <div class="leftside">
          <!-- <p>{{ MemberName }}</p> -->
          <div class="leftnav">
            <router-link to="/account/memberInfo" class="iconBg">
              <div class="innerStyle">
                <span>{{ $t("Account.MemberInformation") }}</span>
              </div>
            </router-link>
            <router-link to="/account/modifyPassword" class="iconBg">
              <div class="innerStyle">
                <span>{{ $t("MemberInfo.ModifyPassword") }}</span>
              </div>
            </router-link>
            <!-- <router-link to="/account/notification" class="iconBg">
              <div class="innerStyle">
                <span>{{ $t("Account.MyMessages") }}</span>
              </div>
            </router-link> -->
            <!-- <router-link to="/account/message" class="iconBg">
              <div class="innerStyle">
                <span>{{ $t("Account.LatestNews") }}</span>
              </div>
            </router-link> -->
            <router-link to="/account/ptxorder" class="iconBg">
              <div class="innerStyle">
                <span>{{ $t("Enquiry.Servicestatus") }}</span>
              </div>
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
import sdk from '@/sdk/InstoreSdk';
@Component
export default class accountHeader extends Vue {
  private MemberName: string = '';
  private TotalPoints: string = '';

  // 获取会员信息
  getMemberInfo() {
    let _this = this;
    sdk.api.member.getProfile().then(function (data) {
      console.log(data, 'datadata');
      _this.MemberName = data.FirstName + '  ' + data.LastName;
    });
  }

  // 获取会员总积分
  getMemberTotal() {
    this.$Api.pointsApi.GetMemberTotalPointsInfo().then((result) => {
      this.TotalPoints = result.data.TotalPoints;
    });
  }

  get currentlang() {
    return this.$i18n.locale;
  }

  mounted() {
    this.getMemberInfo();
    this.getMemberTotal();
  }
}
</script>

<style scoped lang="less">
.accountHeader {
  width: 100%;
  display: flex;
  .memberBg {
    box-sizing: border-box;
    display: inline-block;
    width: 100%;
    padding-top: 150px;
    padding-bottom: 30px;
    // background: url("/images/pc/pcbg.png") no-repeat center center;
    background-size: cover;
    .innerBox {
      width: 100%;
      margin: 0 auto;
      .memberCenterTitle {
        justify-content: left;
    margin-bottom: 45px;
    width: auto;
    height: 40px;
    text-align: left;
    padding-top: 0;
    .center{
      position: relative;
      color: #333333;
      font-family: 'SourceHanSerifCN-Bold' !important;
      background-image: none;
      height: auto;
      padding-left: 24px;
      font-size: 48px;
      line-height: 50px;
      width: auto;
      padding-top: 0;
      &::before{
        content: '';
        width: 16px;
        height: 16px;
        background-color: #3c408e;
        position: absolute;
        left: 0;
        bottom: -24px;
        transform: rotateZ(45deg);
      }
      &::after{
        content: '';
        width: 300px;
        height: 2px;
        background-color: #3c408e;
        position: absolute;
        bottom: -17px;
        left: 16px;
        transform:translateX(0);
      }
    }
      }
      .leftside {
        width: 280px;
        padding-top: 30px;
        p {
          font-size: 24px;
          color: #333;
          margin-bottom: 30px;
        }
        a {
          display: inline-block;
          &:last-child {
            margin-right: 0px !important;
          }
          img {
            width: 100%;
          }
        }
      }

      .iconBg {
        border: 2px solid #999999;
        background-size: contain;
        float: left;
        width: 100%;
        height: 60px;
        text-align: center;
        // padding: 10px;
        margin-bottom: 40px;
        box-sizing: border-box;
        border-radius: 10px;
        // margin-right: 2.66%;
        &:last-child {
          margin-bottom: 0px!important;
        }
        .innerStyle {
          // background: #fff;
          display: block;
          width: 100%;
          height: 100%;
          display: flex;
          align-items: center;
          justify-content: center;
        }
        &:hover {
          transform: translateY(-3px);
        }
        span {
          width: 100%;
          display: inline-block;
          color: #666666;
          font-size: 32px;
          font-weight: 500;
          text-transform: uppercase;
          padding: 10px;
          letter-spacing: 4px;
          margin-top: -5px;
        }
        strong {
          width: 100%;
          display: inline-block;
          color: #262626;
          font-size: 24px;
          font-weight: 600;
        }
        b {
          width: 100%;
          display: inline-block;
          color: #262626;
          font-size: 14px;
          font-weight: 600;
        }
      }

      .router-link-active {
        border: 2px solid @base_color;
        background-color: @base_color;
        border-radius: 10px;
        background-size: contain;
        float: left;
        height: 60px !important;
        text-align: center;
        position: relative;
        // padding: 10px;
        .innerStyle {
          // background: @base_color;
          display: block;
          width: 100%;
          height: 100%;
          display: flex;
          align-items: center;
          justify-content: center;
        }
        span {
          // font-weight: 600 !important;
          color: #fff;
          margin-top: -5px;
        }
        &::after{
          content: '';
          width: 0;
          height: 0;
          border-top: 12px solid transparent;
          border-left: 12px solid #3c408e;
          border-right: 12px solid transparent;
          border-bottom: 12px solid transparent;
          position: absolute;
          right: -34px;
          top: 50%;
          transform: translateY(-50%);
        }
      }
    }
  }
}
.ENG{
  .memberBg .innerBox .memberCenterTitle .center{
    font-size: 36px;
  }
  .memberBg .innerBox .iconBg span{
    font-size: 20px;
  }
}
</style>
