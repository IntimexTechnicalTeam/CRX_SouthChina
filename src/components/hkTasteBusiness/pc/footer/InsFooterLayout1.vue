<template>
  <div id="footer">
    <div class="footer" v-if="routerPath === '/'">
      <div class="InnerBox" :class="{'CNHeight':currentlang=='C'}" >
      <div class="InnerBox_w">
        <div class="left">
          <div class="footerNav">
                <ul v-for="(n,index) in footerMenus" :key="index">
                  <li>
                      <a href="javascript:;" v-if="n.Type === 0" @click="toUrl(n)"><span>{{n.Name}}</span></a>
                      <router-link :to="To(n)"  v-else><span>{{n.Name}}</span></router-link>
                    <!-- <ul>
                      <li v-for="(c,index2) in n.Childs" :key="index2">
                        <a href="javascript:;" v-if="c.Type === 0" @click="toUrl(c)">
                                {{c.Name}}
                          </a>
                        <router-link :to="To(c)" v-else>{{c.Name}}</router-link>
                      </li>
                    </ul> -->
                  </li>
              </ul>
            </div>
              <!-- <div class="topImg">
              <img src="/images/mobile/footerbg.png">
            </div> -->
            <!-- <div class="FooterContactUs" v-html="FooterContactUs"></div> -->
        </div>
        <div class="right">
            <a href="/"><img src="/images/pc/PCF_logo.png"></a>
        </div>
      </div>

    </div>
    </div>

    <div class="footerCpy">
      <p class="footerText">Copyright {{currentYear}}© South China CPA Limited. Al Right Reserved | Powered by Eventizer<a href="https://eventizer.hk/" target="_blank"><img src="/images/mobile/footerlogo.png"></a></p>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component({
  components: {
    Menu: () => import('@/components/business/mobile/header/InsElMenu.vue')
  }
})
export default class InsFooterLayout1 extends Vue {
  currentYear: number = 0;
  footerMenus: any[] = [];
  FooterContactUs: string = '';

  get routerPath() {
    return this.$route.path;
  }
  goToTop() {
    let sTop = document.documentElement.scrollTop;
    let times = setInterval(() => {
      sTop -= 50;
      if (sTop <= 0) {
        document.documentElement.scrollTop = 0;
        clearInterval(times);
      } else {
        document.documentElement.scrollTop = sTop;
      }
    }, 1);
  }
  getFooterContactUs() {
    this.$Api.cms
      .getContentByDevice({ Key: 'ContactUs', IsMobile: true })
      .then((result) => {
        this.FooterContactUs = result.CMS.Body;
      });
  }
  toUrl (n) {
    if (!n.IsNewWin && n.Url) {
      window.location.href = n.Url;
    } else if (n.IsNewWin && n.Url) {
      window.open(n.Url);
    }
  }
  To (n) {
    return n.Type === 1 ? '/cms/catDetail/' + n.Value.Id : n.Type === 2 ? '/CMS/content/' + n.Value.Id : n.Type === 3 ? '/RegNPay/Form/' + n.Value.Id : n.Type === 4 && !this.$store.state.catMenuType ? '/product/cat/' + n.Value.Id : n.Type === 4 && this.$store.state.catMenuType ? '/product/search/-?catalogs=' + JSON.stringify([parseInt(n.Value.Id)]) + '&type=0' : n.Type === 5 ? '/product/search/-?attrs=' + JSON.stringify([{ Id: parseInt(n.Value.Id), Vals: [] }]) + '&type=0' : '/product/search/-?attrs=' + JSON.stringify([{ Id: parseInt(n.ParentId), Vals: [parseInt(n.Value.Id)] }]) + '&type=0';
  }
  getMenu () {
    this.$Api.promotion.getMenu().then((result) => {
      this.footerMenus = result.ReturnValue.FooterMenus;
    });
  }
  get currentlang () {
    return this.$i18n.locale;
  }
  created() {
    var date = new Date();
    this.getFooterContactUs();
    this.currentYear = date.getFullYear();
    this.getMenu();
  }
}
</script>

<style scoped lang="less">
/* 底部文件 */
#footer {
  width: 100%;
  display: inline-block;

  // margin-top: -4rem;
  // background: url('/images/pc/pc_11.png') top center repeat-x;
  .footer{
    border-top: 10px solid #999999;
  }
  .InnerBox {
    width: 100%;
    height: 320px;
    background: url('/images/pc/PCindexback_10.png') top center repeat-x;
    background-size: cover;
    position: relative;
    .InnerBox_w{
      width: 1200px;
      margin: 0 auto;
      display: flex;
      justify-content: space-between;
      // height: 500px;
      // padding-top: 4rem;
    }
    .left {
      width: 50%;
      // position: relative;
      &::after{
        content: '';
        width: 2px;
        height: 250px;
        background-color: #fff;
        position: absolute;
        right: 50%;
        top: 50%;
        transform: translate(-50%,-50%);
      }
        .topImg {
          position: absolute;
          width: 100%;
          left: 0px;
          top: -3.5rem;
          z-index: 99;
          img {
            width: 100%;
          }
        }
      .FooterContactUs {
        width: 100%;
        display: inline-block;
        text-align: center;
        padding-top: 3rem;
        /deep/ .PtxtContactMain {
          width: 100%;
          display: inline-block;
          text-align: center;
          .title {
            display: flex;
            align-items: center;
            justify-content: center;
            .IconA {
              width: 30px;
              height: 30px;
              display: inline-block;
              background: url('/images/mobile/m_40.jpg') no-repeat center center;
              background-size: 30px;
            }
            .IconB {
              width: 30px;
              height: 30px;
              display: inline-block;
              background: url('/images/mobile/m_41.jpg') no-repeat center center;
              background-size: 30px;
            }
            .IconC {
              width: 30px;
              height: 30px;
              display: inline-block;
              background: url('/images/mobile/m_42.jpg') no-repeat center center;
              background-size: 30px;
            }
            .IconD {
              width: 30px;
              height: 30px;
              display: inline-block;
              background: url('/images/mobile/m_43.jpg') no-repeat center center;
              background-size: 30px;
            }
            .text {
              font-size: 18px;
              color: @base_color;
              font-style: normal;
              padding-left: .5rem;
              font-family: 'Domine-Bold','SourceHanSerifCN-Bold'!important;
            }
          }
          .content {
            font-size: 18px;
            margin-top: 1rem;
            margin-bottom: 2rem;
            color: #666666;
          }
        }
      }
    }
    .left {
      width: 50%;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 320px;
      .footerNav{
          width: 100%;
          justify-content: space-between;
          // display: flex;
          padding: 0 40px;
      }
      .footerNav > ul{
          // float: left;
          // width: 20%;
          // &:last-child {
          //   margin-right: 0px!important;
          // }
      }
      .footerNav > ul >li{
        width: 50%;
        position: relative;
        text-align: left;
        float: left;
        margin: 16px 0;

      }
      .footerNav > ul >li >a{
          font-size:20px;
          color:#fff;
          font-family: 'SourceHanSerifCN-Bold';
          border-bottom: 1px solid #fff;
          padding-bottom: 5px;
          &:hover {
            color: #fff;
          }
      }
      .footerNav > ul >li >a >span{
        //   height: 40px;
        // display: block;
      }
      .footerNav > ul >li >ul{
          position: absolute;
          transform: translate(-50%);
          line-height: 20px;
          width: 200%;
          left: 50%;
      }
      .footerNav > ul >li >ul a{
          font-size: 18px;
          color:#999999;
          display: inline-block;
          padding-bottom: 10px;
      }
      .footerNav > ul >li >ul a:hover{
        text-decoration: underline;
        color: @base_color;;
      }
    }
    .right{
      width: 50%;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 320px;
    }
  }
  // .CNHeight {
  //   height: 400px!important;
  // }
  .footerCpy {
    width: 100%;
    // margin-top: 50px;
    // padding-top: 10px;
    // padding-bottom: 10px;
    // border-top: 1px solid #fff;
    background: #ffffff;
    height: 50px;
    display: flex;
    align-items: center;
    .footerText{
      text-align: center;
      color: #666666;
      font-size: 14px;
      width: 90%;
      margin: 0 auto;
      display: flex;
      align-items: center;
      justify-content: center;
      text-align: center;
      font-family: Arial;
      a {
        margin-left: 5px;
        img{
          width: 70px;
        }
      }
    }
  }
}
</style>
