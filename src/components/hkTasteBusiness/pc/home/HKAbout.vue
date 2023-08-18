<template>
  <div class="ContainerMain" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="HomeAbout">

      <div class="Content">

        <div  class="AboutContent">
          <div class="NormalTitle">
            <!-- <i class="left"></i> -->
            <span class="center">{{ AboutUs.Title }}</span>
            <!-- <i class="right"></i> -->
          </div>
          <div v-html="AboutUs.Body"></div>
          <div class="btn">
            <a href="/CMS/content/20295">{{$t('Calendar.KnowMore')}}</a>
          </div>
        </div>
        <div class="right">
              <img :src="AboutUs.Cover" />
        </div>
      </div>
    </div>
    <div class="HomeService">
      <div class="HomeServiceback">
        <div class="NormalTitle">
          <i class="left"></i><span class="center">{{ ServiceTitle }}</span
          ><i class="right"></i>
        </div>
        <div class="ServiceMain">
          <ul>
            <li v-for="(v, index) in OurService" :key="index">
              <router-link :to="'/cms/contentN/'+v.Id">
                <p class="Cover">
                  <img :src="v.Cover" />
                </p>
                <div class="box">
                  <p class="Title">{{ v.Title }}</p>
                  <p class="Title">{{ v.Desc }}</p>
                </div>
              </router-link>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <div class="HomeBusinessScope">
      <div class="NormalTitle">
        <i class="left"></i><span class="center">{{ BusinessScopeTitle }}</span
        ><i class="right"></i>
      </div>
      <div class="Content" v-html="BusinessScopecontent"></div>
      <div class="BusinessScopeMain fix">
        <ul class="right">
          <li>
            <div class="Titleone">
              {{$t('home.service')}}
            </div>
            <div class="textone">

              {{$t('home.Price')}}
            </div>
          </li>
          <li v-for="(v, index) in BusinessScope" :key="index">
            <div class="Title">
              <p>{{ v.Title }}</p>
            </div>
            <div class="text">
              <div class="Desc" v-html="v.Body">
              </div>
              <div class="btn" @click="getshow(v.Key)">
                {{$t('home.Select')}}
              </div>
            </div>
          </li>
        </ul>
      </div>
      <div class="auditform">
        <div class="FormMain" v-show="cmsKey!==null">
          <div v-html="htmlString" class="to_vertical" id="content"></div>
          <div id="preview" style="display: none"></div>
        </div>
      </div>
    </div>
    <div class="Supply">
      <router-link class="img" to="/Enquiry/InsertBider"><img :src="supplier.Cover" width="100%"></router-link>
    </div>
    <div class="HKLiveBox">
      <div class="NormalTitle">
        <i class="left"></i><span class="center">{{ contactusname }}</span
        ><i class="right"></i>
      </div>
      <div class="LiveBoxMain">
        <!-- <div class="FormMain">
          <div v-html="htmlString" class="to_vertical" id="content"></div>
          <div id="preview" style="display: none"></div>
        </div> -->
        <p v-html="Facebook" class="Facebook"></p>

      </div>
    </div>
    <div class="hkmaps">
        <p v-html="IG" class="IG"></p>
    </div>
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component
export default class PkLiveBox extends Vue {
  AboutUs: string = '';
  OurService: any = [];
  ServiceTitle: string = '';
  BusinessScope: any = [];
  BusinessScopeTitle: string = '';
  BusinessScopecontent: string = '';
  MajorClient: any = [];
  MajorClientTitle: string = '';
  JoinUs: string = '';
  Facebook: string = '';
  contactusname:string = '';
  IG: string = '';
  private htmlString: string = '';
  Signer: any = null;
  FormTitle: string = '';
  supplier:string='';
  private navId:any = 0;
  cmsKey: string='';
  FormId: string = '';
  swiperHomeMajorClient: any = {
    autoplay: {
      disableOnInteraction: false
    },
    pagination: {
      el: '.swiper-pagination',
      clickable: true
    },
    slidesPerView: 4,
    spaceBetween: 20,
    autoHeight: true, // 高度随内容变化
    observer: true, // 修改swiper自己或子元素时，自动初始化swiper
    observeParents: true // 修改swiper的父元素时，自动初始化swiper
  };
  getAbout() {
    this.$Api.cms
      .getContentByDevice({ Key: 'HomeAbout', IsMobile: false })
      .then((result) => {
        this.AboutUs = result.CMS;
      });
    this.$Api.cms
      .getContentByDevice({ Key: 'supplier', IsMobile: false })
      .then((result) => {
        this.supplier = result.CMS;
      });
  }
  getService() {
    //  this.$Api.promotion.getPromotion('Home', 1).then(result => {
    //     this.ServiceTitle = result.Promotion.Desc;
    //     this.OurService = result.Promotion.PrmtProductList;
    //     console.log(result, 'eeeee');
    // });
    this.$Api.cms.getCategoryByDevice({ Key: 'OurService', IsMobile: false }).then(async (result) => {
      console.log(result, 'eeeee');
      this.OurService = result.Contents;
      this.ServiceTitle = result.Name;
    });
  }
  getMedia() {
    this.$Api.cms
      .getContentByDevice({ Key: 'contactus', IsMobile: false })
      .then((result) => {
        this.Facebook = result.CMS.Body;
        this.contactusname = result.CMS.Title;
      });
    this.$Api.cms
      .getContentByDevice({ Key: 'map', IsMobile: false })
      .then((result) => {
        this.IG = result.CMS.Body;
      });
  }

  getBusinessScope() {
    this.$Api.cms
      .getCategoryByDevice({ Key: 'Onlineaudit', IsMobile: false })
      .then(async (result) => {
        console.log(result, 'eeeee');
        this.BusinessScope = result.Contents;
        this.BusinessScopeTitle = result.Name;
        this.BusinessScopecontent = result.Content;
        // for (var i = 0; i < result.Contents.length; i++) {
        //   // this.getForm(result.Contents[i].Key);
        //   this.cmsKey = result.Contents[i].Key;
        // }
        console.log(this.BusinessScope, 'this.BusinessScope');
      });
  }
  getshow(regKey) {
    console.log(regKey, 'regKey');
    this.cmsKey = regKey;
    // alert(1);
    this.getForm();
  }
  get isLogin() {
      return this.$store.state.isLogin;
    }
  getForm() {
    console.log(this.cmsKey, 'this.cmsKey');
    this.$Api.regAndPay
      .getHtml(this.cmsKey, this.lang, false)
      .then((result) => {
        this.htmlString = result.HtmlString;
        this.FormTitle = result.Title;
        console.log(result, 'Form');
        // if (result.IsLogin === true) {
        //   if (this.isLogin === 0) {
        //     this.$router.push('/account/login?returnurl=/CMS/content/' + this.$route.params.id);
        //   } else {
        //     this.$router.push('/CMS/content/' + this.$route.params.id);
        //   }
        // }
        this.$nextTick(() => {
          if (document.querySelectorAll('#Sign').length > 0) {
            this.Signer = new intimex.CanvasSigner(
              '#NewSignCanvas',
              '#Signature',
              {
                color: '#58B63A',
                width: 5
              }
            );
            this.Signer.initCanvas();
            window['Signer'] = this.Signer;
          }
        });
      });
  }
 Regnay () {
    window['jsData'] = {
      HasPreview: true,
      UploadButtonText: this.$t('RegNPay.UploadButtonText'),
      UploadingText: this.$t('RegNPay.UploadingText'),
      UploadSuccessfulText: this.$t('RegNPay.UploadSuccessfulText'),
      UploadFailText: this.$t('RegNPay.UploadFailText'),
      NoFileText: this.$t('RegNPay.NoFileText'),
      UploadLengthText: this.$t('RegNPay.UploadLengthText'),
      UploadSizeText: this.$t('RegNPay.UploadSizeText'),
      BackText: this.$t('RegNPay.BackText'),
      ConfirmText: this.$t('RegNPay.ConfirmText'),
      PleaseSelect: this.$t('RegNPay.PleaseSelect'),
      PreviewTitleText: this.$t('RegNPay.PreviewTitleText'),
      RequiredText: this.$t('RegNPay.RequiredText'),
      FormatErrorText: this.$t('RegNPay.FormatErrorText'),
      Version: '2.0',
      HasRNPConfirm: false
    };
    this.$LoadScript('/static/js/CanvasSigner.js');
    this.$LoadScript('/static/js/ajaxFileUpload.js');

    document.dispatchEvent(new Event('rnpFinshed'));

    // RNP Form后台预览跳转语言判断
    if (this.queryLang) {
        this.$Api.member.setUILanguage(this.queryLang).then((result) => {
        this.$i18n.locale = this.queryLang as string;
        localStorage.setItem('locale', this.queryLang as string);
        this.getForm();
      }).catch((error) => {
        console.log(error);
      });
    } else {
      this.getForm();
    }
  }
  get lang() {
    return this.$Storage.get('locale');
  }
  get queryLang () {
    return this.$route.query.Lang || '';
  }
  created() {
    this.cmsKey = 'Auditassurance';
    this.getAbout();
    this.getService();
    this.getBusinessScope();
    this.getMedia();
    this.Regnay();
  }
  mounted() {
    window['regAndPay'] = this.$Api.regAndPay;
    window['router'] = this.$router;
    // window['getPanel'] = this.$Api.getPanel;
    window['Elalert'] = this.$alert;
  }
}
</script>
<style  lang="less" scoped>
.ContainerMain {
  width: 100%;
  display: inline-block;
  .HomeAbout {
    width: 1200px;
    margin: 0 auto;
    margin-top: 50px;
    .Content {
      width: 100%;
      display: flex;
      justify-content: space-between;
      .right {
        width: 560px;
        img {
          width: 100%;
          // border-radius: 5px;
          overflow: hidden;
          display: block;
        }
      }
      .AboutContent {
        width: 49%;
        padding-left: 20px;
        /deep/ p {
          font-size: 24px;
          color: #333333;
          line-height: 34px;
          text-align: justify;
          padding-right: 95px;
          display: -webkit-box;
          overflow: hidden;
          -webkit-line-clamp: 3;
          -webkit-box-orient: vertical;
          word-wrap: break-word;
        }
        .NormalTitle{
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
            font-size: 36px;
            line-height: 40px;
            width: auto;
            padding-top: 0;
            &::before{
              content: '';
              width: 18px;
              height: 18px;
              background-color: #3c408e;
              position: absolute;
              left: 0;
              bottom: -24px;
              transform: rotateZ(45deg);
            }
            &::after{
              content: '';
              width: 234px;
              height: 2px;
              background-color: #3c408e;
              position: absolute;
              bottom: -16px;
              left: 20px;
              transform:translateX(0);
            }
          }
        }
        .btn{
          margin-top: 30px;
          display: block;
          a{
            border: 2px solid #3c408e;
            padding: 4px 36px;
            border-radius: 18px;
            color: #3c408e;
            font-size: 18px;
            font-weight: bold;
            box-sizing: border-box;
            line-height: 38px;
          }
        }
      }
    }
  }
  .HomeService {
    width: 100%;
    height: 560px;
    background-color: #f8f8f8;
    position: relative;
    background: #f8f8f8 url('/images/pc/indexback_02.png') no-repeat center center;
    background-size: contain;
    background-position: bottom;
    margin-top: 80px;
    // &::after{
    //   content: '';
    //   width: 90%;
    //   height: 352px;
    //   background: url('/images/pc/indexback_02.png') no-repeat center center;
    //   background-size: contain;
    //   position: absolute;
    //   left: 50%;
    //   transform: translateX(-50%);
    //   bottom: 0;
    // }
    .HomeServiceback{
      width: 1200px;
      margin: 0 auto;
    }

    ul {
      width: 100%;
      display: inline-block;
      li {
        width: 220px;
        float: left;
        margin-right: 25px;
        // margin-bottom: 2.5%;
        position: relative;
        &:nth-child(5n) {
          margin-right: 0px !important;
        }
        a {
         font-size: 16px;
         color: #2c3e50;
         transition: all 0.3s;
         .box .Title{
          transition: all 0.3s;
         }
         &:hover {
          .Title {
            color: @base_color;
          }
          .Cover{
            background: #eee;
          }
         }
        }
        .Cover {
          width: 100%;
          transition: all .5s;
          img {
            width: 100%;
            margin: 0 auto;
            display: flex;
          }
        }
        .Title {
          width: 90%;
          margin: 0 auto;
          text-align: center;
          font-size: 18px;
          line-height: 24px;
          // margin-top: 1rem;
          color: #ffffff;
          &:first-child{
            font-size: 20px;
            margin-bottom: 8px;
          }
        }
        .box{
          position: absolute;
          left: 50%;
          transform: translateX(-50%);
          width: 100%;
          bottom: 44px;
        }
      }
    }
  }
  .HomeBusinessScope {
    width: 1200px;
    display: block;
    margin: 0 auto;
    margin-bottom: 80px;
    // margin-top: 3rem;
    // background: url("/images/pc/pc_03.jpg") no-repeat center center;
    // padding-top: 3rem;
    // padding-bottom: 3rem;
    background-size: cover;
    .Content{
      /deep/ p{
        padding: 0 34px;
        box-sizing: border-box;
        font-size: 24px;
        color: #333333;
        line-height: 30px;
        letter-spacing: 2px;
      }
    }
    .BusinessScopeMain{
      margin-top: 20px;
      ul.left{
        width: 200px;
        float: left;
        border-right: 1px solid #cccccc;
        box-sizing: border-box;
        li:nth-child(1){
          height: 80px;
          background-color: #cecece;
          line-height: 80px;
          text-align: center;
          font-size: 24px;
          color: #333333;
        }
        li:nth-child(2){
          height: 150px;
          background-color: #f5f5f5;
          padding-top: 24px;
          text-align: center;
          font-size: 24px;
          color: #333333;
          box-sizing: border-box;
        }
      }
      ul.right {
        float: left;
      width: 1200px;
      margin: 0 auto;
      // display: flex;
      // flex-wrap: wrap;
      // margin-top: 2rem;
      li {
        width: 200px;
        float: left;
        position: relative;
        transition: all 0.3s;
        // margin-right: 2%;
        // margin-bottom: 2%;
        // &:nth-child(6n) {
        //   margin-right: 0px !important;
        // }
        &:first-child{
          border-right: 1px solid #cccccc;
          box-sizing: border-box;
        }
        .Titleone{
          line-height: 80px;
          text-align: center;
          font-size: 24px;
          color: #333333;
          // background-color: #cecece;
          background: url('/images/pc/serviceback.png');
        }
        .textone{
          height: 150px;
          background-color: #f5f5f5;
          padding-top: 24px;
          text-align: center;
          font-size: 24px;
          color: #333333;
          box-sizing: border-box;
        }
        .Title{
          height: 80px;
          // background-color: #cecece;
          background: url('/images/pc/serviceback.png');

          transition: all 0.3s;
          p{
            line-height: 80px;
          text-align: center;
          font-size: 24px;
          color: #333333;
          position: relative;
          z-index: 1;
          transition: all 0.3s;
          }
        }
        .text{
          height: 150px;
          background-color: #f5f5f5;

          text-align: center;
          transition: all 0.3s;
          .Desc{
            color: #333333;
            line-height: 24px;
            font-size: 20px;
            text-align: center;
            padding-top: 18px;
            z-index: 1;
            position: relative;
            transition: all 0.3s;
          }
          .btn{
            border-radius: 20px;
            border: 2px solid #3c408e;
            width: 102px;
            height: 40px;
            box-sizing: border-box;
            margin: 0 auto;
            font-size: 20px;
            color: #3c408e;
            line-height: 36px;
            margin-top: 15px;
            z-index: 1;
            position: relative;
            cursor: pointer;
          }
        }
        .Cover {
          width: 100%;
          img {
            width: 90%;
            margin: 0 auto;
            display: flex;
          }
        }
        &:hover{

          .Title::after{
            content: '';
            width: 200px;
            height: 80px;
            background-color: rgba(230, 199, 123, 0.6);
            position: absolute;
            top: 0;
            left: 0;
          }
          .text::after{
            content: '';
            width: 200px;
            height: 150px;
            background-color: rgba(230, 199, 123, 0.6);
            position: absolute;
            bottom: 0;
            left: 0;

          }
          .btn{
            background-color: #3c408e;
            color: #fff;
          }
            // width: 200px;
            // height: 230px;
            // background-color: rgba(230, 199, 123, 0.6);
            // position: absolute;
            // top: 0;
            // left: 0;

          }
      }
    }
    }
    .auditform{
      .FormMain{
        width: 800px;
        margin: 0 auto;
        margin-top: 28px;
        box-shadow: 0 6px 10px #d9d9d9;
        padding: 30px 80px;
        box-sizing: border-box;
        /deep/ #content {
          position: relative;
          .form-group{
            text-align: right;
          }
          .btn.save {
            border: 0;
            // width: 100%;
            background: @base_color;
            border-radius: 30px;
            font-size: 20px;
            height: 40px;
            margin: 0;
            font-weight: 500 !important;
            color: #fff;
            font-family: 'SourceHanSerifCN-Regular';
            padding: 0 50px;
            position: relative;
            text-align: right;

          }
          .col-md-offset-2 {
            position: absolute;
            right: 0;
            bottom: 0;
            &::after{
              content: '';
              width: 18px;
              height: 16px;
              background: url('/images/pc/rightside.png') no-repeat;
              position: absolute;
              top: 50%;
              transform: translateY(-50%);
              right: 20px;
            }
          }
          .content {
            position: relative;
            form > .pay{
              text-align: right;
              font-size: 18px;
              display: none;
            }
          }
          #Anwers {

                  display: flex;
                  flex-wrap: wrap;
                  justify-content: space-between;
                  margin:0!important;
                  p[name="error"]{
                    // margin:3px 0!important;
                    // margin-bottom: 5px;
                    // padding-left:5px;
                    color: red;
                    font-size: 14px;
                    margin-left: 152px;
                    text-align: left;
                  }
                  .form-group {
                    padding: 0;
                    border: 0;
                    width: 100%;
                    // margin-bottom:4%!important;
                    background:transparent!important;
                    // display: flex;
                    // justify-content: space-between;
                    margin-bottom: 14px;
                    align-items: center;
                    &:nth-child(5), &:nth-child(6){
                        width: 50%;
                        float: left;
                        // display: flex;
                        // align-items: stretch;
                        // height: 50px;

                    }
                    &:nth-child(5){
                      .control-label{
                          opacity: 0;
                          display: none;
                        }
                      fieldset.files{
                        width: 290px;
                        display: block;
                        // height: 50px;
                        // line-height: 50px;
                        text-align: center;
                        input[type="file"]{
                          box-sizing: border-box;
                          // padding:0 0 0 15px!important;
                          border:2px solid #999999!important;
                          font-family: 'SourceHanSerifCN-Regular';
                          font-size: 18px;
                          background:#fff;
                          height: 50px;
                          outline: none;
                          width: 100%;
                          border-radius: 10px;
                          line-height: 44px;
                          text-align: center;
                          padding-left: 10px;
                        }
                        .box{
                          // margin-bottom: 10px;
                        }
                        .closeX{
                          color: #fff;
                          margin-left: 10px;
                          background-color: red;
                          border-radius: 50%;
                          width: 20px;
                          height: 20px;
                          display: inline-block;
                          font-size: 13px;
                        }
                        .uploadMain{
                          background-color: #999999;
                          border-radius: 5px;
                          // width: 140px;
                          height: 30px;
                          // display: flex;
                          justify-content: space-between;
                          color:#fff;
                          font-size: 18px;

                          text-align: center;
                          position: relative;
                          box-sizing: border-box;
                          margin: 0 auto;
                          margin-top: 8px;
                          display: inline-block;
                          padding: 0 10px;
                          padding-left: 36px;
                          i{
                            width: 14px;
                            height: 16px;
                            background: url('/images/pc/uploadicon.png') no-repeat center left;
                            background-size: contain;
                            position: absolute;
                            top: 50%;
                            transform: translateY(-50%);
                            left: 20px;
                          }
                        }
                      }
                      p[name="error"]{
                        margin-left: 30px;
                        margin-top: 75px;
                      }
                    }
                    &:nth-child(6){
                          margin-bottom: 0;
                      .control-label{
                          width: 100px;
                          padding-right: 0;
                          display: block;
                          line-height: 50px;
                        }
                      fieldset{
                        width: 68%;
                        height: 50px;
                        line-height: 50px;
                      }
                      p[name="error"]{
                        margin-left: 114px;
                      }
                    }

                    .control-label{
                      font-size:18px;
                      color:#000!important;
                      font-family: 'SourceHanSerifCN-Regular';
                      font-weight: normal;
                      // margin-bottom: 20px;
                      width: 130px;
                      text-align: right;
                      padding-right: 20px;
                      float: left;
                      display: flex;
                      justify-content: right;
                      align-items: center;
                      height: 50px;
                      // box-sizing: border-box;
                      .control-label-remark{
                        display:none;
                      }
                    }

                    ::-webkit-input-placeholder{
                      color: rgba(102,102,102,1)!important;
                    }
                    ::-moz-placeholder{   /* Mozilla Firefox 19+ */
                     color: rgba(102,102,102,1)!important;
                    }
                    :-moz-placeholder{    /* Mozilla Firefox 4 to 18 */
                      color: rgba(102,102,102,1)!important;
                    }
                    :-ms-input-placeholder{  /* Internet Explorer 10-11 */
                      color: rgba(102,102,102,1)!important;
                    }

                    fieldset {
                      // background-color: #fff;
                      border: 0;
                      width: 490px;
                      padding: 0;
                      margin: 0;
                      display: block;
                      float: right;
                      // margin-bottom: 14px;
                      // height: 40px;
                      input[type="text"],[type="email"]{
                        // height:60px!important;
                        box-sizing: border-box;
                        padding:0 0 0 15px!important;
                        border:2px solid #999999!important;
                       font-family: 'SourceHanSerifCN-Regular';
                        font-size: 18px;
                        background:#fff;
                        height: 50px;
                        outline: none;
                        width: 100%;
                        border-radius: 10px;
                      }

                      textarea{
                        font-family: 'SourceHanSerifCN-Regular';
                        font-size: 18px;
                        // height:180px!important;
                        box-sizing: border-box;
                        background:#fff;
                        border:0!important;
                        width: 100%;
                        outline: none;
                        padding:15px 0 0 15px!important;
                        min-height: 180px;
                      }
                      &.checkbox{
                        margin-bottom: 16px;
                        input{
                          background:#fff;
                        }
                        .option{
                          width:33.33%;
                          float:left;
                          margin-bottom: 10px;
                          label{
                            span{
                              font-size:18px;
                              color:#000!important;
                            }
                          }
                          // &:nth-of-type(odd){
                          //   margin-right:4%;
                          // }
                          input[type="checkbox"]{
                            margin-right:10px !important;
                            border: none;
                            width: 20px;
                            height: 20px;
                            vertical-align: top;
                            appearance: none;
                            outline: none;
                            position: relative;
                            &:checked {
                              border: 1.5px solid #3c408e;
                              background-color: #3c408e;
                            }
                            &:after{
                              position: absolute;
                              content: "";
                              width: 5px;
                              height: 10px;
                              top: 1px;
                              left: 5px;
                              border: 2px solid #fff;
                              border-top: none;
                              border-left: none;
                              transform: rotate(45deg);
                            }
                          }
                        }
                      }
                    }
                  }
                  .only_tilte{
                      h4.control-label{
                        font-size: 30px;
                        width: 100%;
                        text-align: center;
                        display: block;
                        font-weight: bold;
                        padding-left: 82px;
                        box-sizing: border-box;
                      }
                    }
                }
        }
        /deep/ #preview{
          .title{
            font-size: 24px;
            text-align: center;
          }
          .question{
            font-size: 18px;
            margin-bottom: 5px;
            color: #333333;
          }
          .anwer{
            margin-bottom: 15px;
            p{
              font-size: 18px;
              color: #3c408e;
            }
          }
          .confirm, .back{
            border: 0;
            // width: 100%;
            background: @base_color;
            border-radius: 30px;
            font-size: 20px;
            height: 40px;
            line-height: 36px;
            margin: 0;
            font-weight: 500 !important;
            color: #fff;
            font-family: 'SourceHanSerifCN-Regular';
            padding: 0 50px;
            position: relative;
            margin-left: 90px;
          }
            // .back{
            //   margin-left: 0;
            // }
        }
      }
    }
  }
  .HomeMajorClient {
    width: 100%;
    display: inline-block;
    padding-top: 3rem;
    padding-bottom: 3rem;
    .SwiperMain {
      width: 1200px;
      margin: 0 auto;
      margin-top: 2rem;
      /deep/ .swiper-container {
        padding-bottom: 3rem;
        .swiper-pagination-bullet {
          background: #e6e6e6;
          opacity: 1;
        }
        .swiper-pagination-bullet-active {
          background: #ffa71a!important;
        }
      }
    }
    .JoinUs {
      width: 1200px;
      margin: 0 auto;
      margin-top: 2rem;
      .JoinUs {
        /deep/ img {
          width: 100%;
        }
      }
    }
  }
  .Supply {
    width: 100%;
    display: flex;
    a{
      display: flex;
      img{
        width: 100%;
        display: flex;
      }
    }
  }
  .HKLiveBox {
    width: 100%;
    height: 475px;
    // display: flex;
    // flex-wrap: wrap;
    background: #f8f8f8 url("/images/pc/indexback_04.png") no-repeat center center;
    // padding-top: 80px;
    // padding-bottom: 6rem;
    background-size: contain;
    box-sizing: border-box;
    background-position: bottom;
    .LiveBoxMain {
      width: 1200px;
      margin: 0 auto;
      display: flex;
      justify-content: space-between;
      .Facebook {
        width: 100%;
        text-align: center;
        /deep/ img {
          width: 100%;
        }
        /deep/.contactBox{
  width: 100%;
  text-align: center;
  p{
    // padding-top: 1.5rem;
    padding-bottom: 10px;
    font-size: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: 'SourceHanSerifCN-Regular';
    color: #333333;
    &:nth-child(1){
      font-size: 24px;
    }
    &:nth-child(5){
      padding-left: 120px;
    }
    &:nth-child(6){
      padding-left: 98px;
    }
    .icon1{
      background: url('/images/pc/PC_index_51.png') no-repeat center center;
      background-size: 100%;
      width: 24px;
      height: 24px;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
    }
    .icon2{
      background: url('/images/pc/PC_index_56.png') no-repeat center center;
      background-size: 100%;
      width: 24px;
      height: 24px;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
    }
    .icon3{
      background: url('/images/pc/PC_index_54.png') no-repeat center center;
      background-size: 100%;
      width: 24px;
      height: 24px;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
    }
    .icon4{
      background: url('/images/pc/PC_index_47.png') no-repeat center center;
      background-size: 100%;
      width: 24px;
      height: 24px;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
    }
    .icon5{
      background: url('/images/pc/PC_index_58.png') no-repeat center center;
      background-size: 100%;
      width: 24px;
      height: 24px;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
    }
  }
        }
      }
      .IG {
        width: 32%;
        text-align: center;
        /deep/ img {
          width: 100%;
        }
      }

    }
  }
  .hkmaps{
    width: 100%;
    height: 400px;
    // overflow: hidden;
  }
}
.ENG{
  .HKLiveBox .LiveBoxMain .Facebook{
    /deep/ .contactBox {
      p:nth-child(4) {
          margin-left: -28px;
      }
      p:nth-child(6) {
          padding-left: 106px;
      }
    }
  }
  .HomeBusinessScope .BusinessScopeMain {
    ul.right {
      li {
        .Title {
          display: flex;
          justify-content: center;
          align-items: center;
          p{
            line-height: 30px;
            word-break: break-word;
          }
        }
      }
    }
  }
  .HomeBusinessScope .auditform .FormMain{
    /deep/ #content #Anwers {
      .form-group {
        .control-label{
          width: 140px;
        }
        fieldset{
          width: 480px;
        }
      }
    }
  }
}
</style>
