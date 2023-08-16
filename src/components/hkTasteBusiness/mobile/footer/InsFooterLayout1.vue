<template>
  <div id="footer" >
    <div class="footerback" v-if="routerPath === '/'">
      <div class="topImg">
        <img src="/images/mobile/PCF_logo.png">
      </div>
      <div class="footerContainer">
            <!-- <div class="FooterContactUs" v-html="FooterContactUs"></div> -->
            <Menu type="footer" class="footerMeun"></Menu>
      </div>

    </div>
    <div class="footerCpy">
        <p class="footerText">Copyright {{currentYear}}© South China CPA Limited. Al Right Reserved</p>
        <p class="footerText">Powered by Eventizer<a href="https://eventizer.hk/" target="_blank"><img src="/images/mobile/footerlogo.png"></a></p>
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
  getMenu() {
    this.$Api.promotion.getMenu().then((result) => {
      this.footerMenus = result.ReturnValue.FooterMenus;
    });
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
  display: block;
  // padding-top: 4rem;
  position: relative;
  .topImg {
    // position: absolute;
    width: 60%;
    margin: 3rem auto;
    // left: 0px;
    // top: -3rem;
    img {
      width: 100%;
      display: block;
    }
  }
  .footerback{
    width: 100%;
    background: url('/images/mobile/mobileindex_12.png') no-repeat center center;
    background-size: cover;
    border-top: 6px solid #999999;
        padding-bottom: 3rem;
  }
  .footerContainer {
    width: 90%;
    margin: 0 auto;
    .FooterContactUs {
      width: 100%;
      display: inline-block;
      text-align: center;
      /deep/ .PtxtContactMain {
        width: 100%;
        display: inline-block;
        text-align: center;
        .title {
          display: flex;
          align-items: center;
          justify-content: center;
          .IconA {
            width: 2.5rem;
            height: 2.5rem;
            display: inline-block;
            background: url('/images/mobile/m_40.jpg') no-repeat center center;
            background-size: 2.5rem;
          }
          .IconB {
            width: 2.5rem;
            height: 2.5rem;
            display: inline-block;
            background: url('/images/mobile/m_41.jpg') no-repeat center center;
            background-size: 2.5rem;
          }
          .IconC {
            width: 2.5rem;
            height: 2.5rem;
            display: inline-block;
            background: url('/images/mobile/m_42.jpg') no-repeat center center;
            background-size: 2.5rem;
          }
          .IconD {
            width: 2.5rem;
            height: 2.5rem;
            display: inline-block;
            background: url('/images/mobile/m_43.jpg') no-repeat center center;
            background-size: 2.5rem;
          }
          .text {
            font-size: 1.4rem;
            color: @base_color;
            font-style: normal;
            padding-left: .5rem;
            font-family: 'Domine-Bold','SourceHanSerifCN-Bold'!important;
          }
        }
        .content {
          font-size: 1.4rem;
          margin-top: 1rem;
          margin-bottom: 2rem;
        }
      }
    }
      /deep/ .el-menu {
        width: 100%;
        margin: 0 auto;
        background-color: transparent !important;
        border: 0;
        // margin-bottom: 1rem;
        // margin-top: 1rem;
        .el-submenu__icon-arrow {
            display: block;
            font-size: 1.6rem;
        }

        > li {
            height: auto;
            line-height: unset;
            text-align: center;
            // margin-bottom: 1rem;
            // border-bottom: 1px solid #ebebeb;
            background-color: transparent !important;
             >a {
                 color:#fff;
                 background-size: 100% 100%;
                 display:block;
                 width: 100%;
                 padding-top: .8rem;
                 padding-bottom: .8rem;
                 margin: 0 auto;
                 font-weight: 500;
                //  text-transform: uppercase;
                 b{
                     color:#fff;
                     display: inline-block;
                    //  width: 100%;
                     font-weight: 500;
                     font-size: 1.4rem;
                     font-family: 'SourceHanSerifCN-Regular'!important;
                     border-bottom: 1px solid #fff;
                     padding-bottom: 0.5rem;
                    //  &:nth-child(1){
                    //     color:#333333;
                    //     font-weight: 500;
                    //     font-size: 1.6rem;
                    //     white-space: normal;
                    //     font-family: 'Domine-Bold','SourceHanSerifCN-Bold'!important;
                    //  }
                    //  &:nth-child(2){
                    //      color:#333333;
                    //      font-size: 1.2rem;
                    //      white-space: normal;
                    //      font-family: 'Domine-Bold','SourceHanSerifCN-Bold'!important;
                    //  }
                 }
            }

            a {
                text-decoration: none;
            }
        }

        li {
            line-height: unset;
            padding: 0 !important;
            min-width: unset;
        }
    .el-submenu__title {
        padding-top: 0.375rem;
        padding-bottom: 0.375rem;
        height: auto!important;
        line-height: unset;
        background-color:#fff!important;
        .name{
            font-size: 1.6rem!important;
            color:#333333;
            font-family: 'Domine-Bold','SourceHanSerifCN-Bold'!important;
        }
        i{
          color: #333333;
        }
    }
    .el-menu--inline {
      width: calc(100% - 2px)!important;
      margin-top: 0px!important;
      border-top: 0px!important;
      box-shadow: 0 2px 5px #ddd;
      >li {
        width: 90%;
        margin: 0 auto;
        &:last-child {
          >a {
            border-bottom: 0px!important;
          }
        }
        >a{
          background: #fff!important;
          border: 0px!important;
          b{
              color: #666666!important;
              font-size: 1.4rem!important;
              font-family: 'SourceHanSerifCN-Regular'!important;
              &:hover {
                color: @base_color!important;
              }
          }
        }
      }
    }
    }
     /deep/ .is-opened > .el-submenu__title{
          color:@base_color!important;
          .name{
              color:@base_color!important;
          }
      }
     /deep/ .is-opened > .el-submenu__title .el-submenu__icon-arrow{
          color:@base_color!important;
      }
  }
  .footerCpy {
    width: 100%;
    padding-top: 10px;
    /* padding-bottom: 10px; */
    background: #ffffff;
    height: 6rem;
    box-sizing: border-box;

    background: #ffffff;
    .footerText{
      text-align: center;
      color: #666666;
      font-size: 1rem;
      width: 94%;
      margin: 0 auto;
      display: flex;
      align-items: center;
      justify-content: center;
      text-align: center;
      font-family: 'SourceHanSerifCN-Regular'!important;
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
