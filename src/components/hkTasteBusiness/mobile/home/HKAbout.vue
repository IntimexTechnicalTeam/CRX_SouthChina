<template>
  <div class="ContainerMain">
    <div class="HomeAbout">

      <div class="NormalTitle">
        <!-- <i class="left"></i> -->
        <span class="center">{{ AboutUs.Title }}</span>
        <!-- <i class="right"></i> -->
      </div>
      <div class="Content">
        <p v-html="AboutUs.Body" class="AboutContent"></p>
      </div>
      <div class="btn">
            <a href="/CMS/content/20295">{{$t('Calendar.KnowMore')}}</a>
          </div>
      <div class="Img"><img :src="AboutUs.Cover" /></div>
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
        <div class="top fix">
          <div class="Titleone">
              {{$t('home.service')}}
            </div>
            <div class="textone">

              {{$t('home.Price')}}
            </div>
        </div>
        <ul class="bottom">
          <li class="fix" v-for="(v, index) in BusinessScope" :key="index">
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
    <div class="HomeMajorClient" style="display:none;">
      <div class="NormalTitle">
        <i class="left"></i><span class="center">{{ MajorClientTitle }}</span
        ><i class="right"></i>
      </div>
      <div class="SwiperMain">
        <swiper
          :options="swiperHomeMajorClient"
          ref="mySwiper"
          v-if="MajorClient.length"
        >
          <!-- slides -->
          <swiper-slide v-for="(slide, index) in MajorClient" :key="index">
            <a
              :href="slide.Url || 'javascript:;'"
              :target="slide.Url ? (slide.IsRedirect ? '_blank' : '_self') : ''"
            >
              <img :src="slide.Cover" width="100%" />
            </a>
          </swiper-slide>
          <!-- Optional controls -->
          <div
            class="swiper-pagination"
            slot="pagination"
            v-if="swiperHomeMajorClient.pagination"
          ></div>
        </swiper>
      </div>
      <div class="JoinUs">
        <div class="NormalTitle">
          <i class="left"></i><span class="center">{{ JoinUs.Title }}</span
          ><i class="right"></i>
        </div>
        <p v-html="JoinUs.Body" class="JoinUs"></p>
      </div>
    </div>
    <div class="Supply">
      <router-link class="img" to="/Enquiry/InsertBider"><img :src="supplier.Cover" width="100%"></router-link>
    </div>
    <div class="HKLiveBox">
      <div class="NormalTitle">
        <i class="left"></i><span class="center">{{ FormTitle }}</span
        ><i class="right"></i>
      </div>
      <!-- <div class="FormMain">
        <div v-html="htmlString" class="to_vertical" id="content"></div>
        <div id="preview" style="display: none"></div>
      </div> -->
      <div class="Main">
        <p v-html="Facebook" class="Facebook"></p>
        <!-- <p v-html="IG" class="IG"></p> -->
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
  IG: string = '';
  private htmlString: string = '';
  Signer: any = null;
  FormTitle: string = '';
  supplier:string='';
  cmsKey: string = '';
  swiperHomeMajorClient: any = {
    autoplay: {
      disableOnInteraction: false
    },
    pagination: {
      el: '.swiper-pagination',
      clickable: true
    },
    slidesPerView: 2,
    spaceBetween: 20,
    autoHeight: true, // 高度随内容变化
    observer: true, // 修改swiper自己或子元素时，自动初始化swiper
    observeParents: true // 修改swiper的父元素时，自动初始化swiper
  };
  getAbout() {
    this.$Api.cms
      .getContentByDevice({ Key: 'HomeAbout', IsMobile: true })
      .then((result) => {
        this.AboutUs = result.CMS;
      });
    this.$Api.cms
      .getContentByDevice({ Key: 'supplier', IsMobile: true })
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
    this.$Api.cms.getCategoryByDevice({ Key: 'OurService', IsMobile: true }).then(async (result) => {
      console.log(result, 'eeeee');
      this.OurService = result.Contents;
      this.ServiceTitle = result.Name;
    });
  }
  getBusinessScope() {
    this.$Api.cms
      .getCategoryByDevice({ Key: 'Onlineaudit', IsMobile: true })
      .then(async (result) => {
        console.log(result, 'eeeee');
        this.BusinessScope = result.Contents;
        this.BusinessScopeTitle = result.Name;
        this.BusinessScopecontent = result.Content;
      });
  }
  getshow(regKey) {
    console.log(regKey, 'regKey');
    this.cmsKey = regKey;
    this.getForm();
  }
  getMedia() {
    this.$Api.cms
      .getContentByDevice({ Key: 'contactus', IsMobile: true })
      .then((result) => {
        this.Facebook = result.CMS.Body;
      });
    this.$Api.cms
      .getContentByDevice({ Key: 'map', IsMobile: true })
      .then((result) => {
        this.IG = result.CMS.Body;
      });
  }
  getForm() {
    this.$Api.regAndPay
      .getHtml(this.cmsKey, this.lang, false)
      .then((result) => {
        this.htmlString = result.HtmlString;
        this.FormTitle = result.Title;
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
    width: 90%;
    margin: 0 auto;
    margin-top: 2rem;
    .Img {
      width: 100%;
      display: block;
      // border-radius: 5px;
      overflow: hidden;
      margin-top: 2.4rem;
      img {
        width: 100%;
        display: block;
      }
    }
    .Content {
      width: 100%;
      display: inline-block;
      .AboutContent {
        /deep/ p {
          font-size: 1.4rem;
          line-height: 2rem;
          display: -webkit-box;
          overflow: hidden;
          -webkit-line-clamp: 3;
          -webkit-box-orient: vertical;
          word-wrap: break-word;
        }
      }
    }
    .NormalTitle{
          justify-content: left;
          margin-bottom: 3rem;
          width: auto;
          height: 3rem;
          text-align: left;
          padding-top: 0;
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
        .btn{
          margin-top: 1rem;
          display: block;
          a{
            border: 2px solid #3c408e;
              padding: 0.3rem 2rem;
              border-radius: 2rem;
              color: #3c408e;
              font-size: 1.4rem;
              font-weight: bold;
              -webkit-box-sizing: border-box;
              box-sizing: border-box;
              // line-height: 34px;
          }
        }
  }
  .HomeService {

    margin-top: 4rem;
    background: #f8f8f8 url('/images/mobile/mobileindex_08.png') no-repeat center center;
    background-size: cover;
    background-position: bottom;
    padding-bottom: 3rem;
    .HomeServiceback{
      width: 90%;
    margin: 0 auto;
    }
    .ServiceMain{
      margin-top: 4rem;
    }
    ul {
      width: 100%;
      display: inline-block;
      li {
        width: 48%;
        float: left;
        margin-right: 4%;
        margin-bottom: 4%;
        position: relative;
        &:nth-child(2n) {
          margin-right: 0px !important;
        }
        a {
         font-size: 1.2rem;
         color: #fff;
        }
        .box{
          position: absolute;
          left: 50%;
          transform: translateX(-50%);
          bottom: 0.5rem;
          width: 100%;
          text-align: center;
        }
        .Cover {
          width: 100%;
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
          font-size: 1.2rem;
          line-height: 1.6rem;
          // margin-top: 1rem;
        }
      }
    }
  }
  .HomeBusinessScope {
    width: 100%;
    display: block;
    margin-top: 3rem;
    padding-bottom: 3rem;
    .Content{
      width: 90%;
      margin: 0 auto;
      /deep/ p{
        font-size: 1.3rem;
        line-height: 2rem;
        color: #333333;
      }
    }
    .BusinessScopeMain{
      width: 90%;
      margin: 0 auto;
      border: 1px solid #cccccc;
      margin-top: 2rem;
      .top{
        .Titleone{
          width: 30%;
          height: 3.5rem;
          line-height: 3.5rem;
          // background-color: #d0d0d0;
          float: left;
          text-align: center;
          font-size: 1.3rem;
          color: #333333;
          background: url('/images/mobile/serviceback.png') right center;
        }
        .textone{
          width: 70%;
          height: 3.5rem;
          line-height: 3.5rem;
          background-color: #f5f5f5;
          float: left;
          font-size: 1.3rem;
          padding-left: 4.4rem;
          box-sizing: border-box;
          color: #333333;
        }
      }
      ul.bottom{
        li{
          border-top: 1px solid #cccccc;
          .Title{
            width: 30%;
            height: 5rem;
            // line-height: 4rem;
            // background-color: #d0d0d0;
            background: url('/images/mobile/serviceback.png') right center;
            float: left;
            text-align: center;
            display: flex;
            justify-content: center;
            align-items: center;
            p{
              font-size: 1.3rem;
              color: #333333;
              word-break: break-word;
            }
          }
          .text{
            width: 70%;
            height: 5rem;
            // line-height: 4rem;
            background-color: #f5f5f5;
            float: left;
            // padding-left: 3rem;
            box-sizing: border-box;
            display: flex;
            align-items: center;
            .Desc{

              width: 55%;
              float: left;
              /deep/ p{
                font-size: 1.3rem;
                color: #333333;
                text-align: center;
              }
            }
            .btn{
              border-radius: 2rem;
              border: 2px solid #3c408e;
              width: 7rem;
              height: 3rem;
              box-sizing: border-box;
              margin: 0 auto;
              font-size: 1.3rem;
              color: #3c408e;
              line-height: 2.6rem;
              z-index: 1;
              position: relative;
              cursor: pointer;
              float: left;
              text-align: center;
            }
          }
        }
      }
    }
    .auditform{
      width: 90%;
      margin: 0 auto;
      box-shadow: 0 6px 10px #d9d9d9;
      margin-top: 2rem;
      .FormMain {
      width: 100%;
      padding: 1.5rem;
      position: relative;
      box-sizing: border-box;
      /deep/ #content {
        .col-md-offset-2{
          text-align: right;
          position: relative;
          &::after{
            content: '';
            width: 16px;
            height: 14px;
            background: url(/images/pc/rightside.png) no-repeat;
            position: absolute;
            background-size: contain;
            top: 50%;
            transform: translateY(-50%);
            right: 2rem;
          }
        }
        .btn.save {
          border: 0;
          width: 50%;
          background: @base_color;
          border-radius: 0;
          font-size: 1.3rem;
          height: 3rem;
          line-height: 3rem;
          margin: 0;
          font-weight: 500 !important;
          color: #fff;
          border-radius: 2rem;
        }

        #Anwers {
          display: flex;
          flex-wrap: wrap;
          .form-group {
            padding: 0;
            border: 0;
            width: 100%;
            margin-bottom: 10px;
            .fieldset {
                border: 0px;
            }
            .checkbox {
              span {
                font-size: 18px;
                color: #666;
              }
            }
            .control-label {
              margin-bottom: 0.5rem !important;
              color: #333333;
              font-size: 1.2rem;
              font-weight: 500;
              width: 30%;
              float: left;
              text-align: right;
              padding-right: 1rem;
              box-sizing: border-box;
                  display: flex;
              justify-content: right;
              align-items: center;
              height: 3.5rem;
              span {
                color: red!important;
              }
            }
            .control-label-remark {
              display: none;
            }

            ::-webkit-input-placeholder {
              color: rgba(102,102,102,1)!important;
            }
            ::-moz-placeholder {
              /* Mozilla Firefox 19+ */
              color: rgba(102,102,102,1)!important;
            }
            :-moz-placeholder {
              /* Mozilla Firefox 4 to 18 */
              color: rgba(102,102,102,1)!important;
            }
            :-ms-input-placeholder {
              /* Internet Explorer 10-11 */
              color: rgba(102,102,102,1)!important;
            }

            fieldset {
              width: 70%;
              float: left;
              &.text {
                input[type="text"] {
                  border: 2px solid #999999;
                  height: 3.5rem;
                  line-height: 3.5rem;
                  padding-left: 1rem;
                  font-size: 1.2rem;
                  box-sizing: border-box;
                  width: 100%;
                  border-radius: 8px;
                  &:focus {
                    border: 1px solid @base_color;
                  }
                }
              }

              &.email {
                input[type="email"] {
                  border: 2px solid #999999;
                  height: 3.5rem;
                  line-height: 3.5rem;
                  padding-left: 1rem;
                  font-size: 1.2rem;
                  box-sizing: border-box;
                  width: 100%;
                  border-radius: 8px;
                  &:focus {
                    border: 1px solid @base_color;
                  }
                }
              }

              &.textarea {
                textarea {
                    height: 9rem;
                    border: 2px solid #999999;
                    padding: 1rem;
                    font-size: 1.2rem;
                    box-sizing: border-box;
                    width: 100%;
                    border-radius: 8px;
                    &:focus {
                    border: 1px solid @base_color;
                    }
                }
              }

            }

            &:nth-child(5){
              // width: 50%;
              // float: left;
              p[name="error"]{
                padding-left: 7.8rem;
              }
              .control-label{
                // display: none;
                opacity: 0;
              }
              .files{
                width: 70%;
                // display: flex;
                justify-content: space-between;
                align-items: center;
                text-align: center;
                input[type="file"]{
                  border: 2px solid #999999;
                  height: 3.5rem;
                  line-height: 3.2rem;
                  padding-left: 0.5rem;
                  font-size: 1.2rem;
                  box-sizing: border-box;
                  width: 100%;
                  border-radius: 8px;
                }
                .uploadMain{
                  // width: 33%;
                  background-color: #999999;
                  border-radius: 5px;
                  display: flex;
                  align-items: center;
                  height: 30px;
                  text-align: center;
                  position: relative;
                  justify-content: center;
                  display: inline-block;
                  padding: 0 1rem;
                  line-height: 26px;
                  margin-top: 10px;
                  i.uploadicon1{
                    width: 14px;
                    height: 16px;
                    background: url('/images/pc/uploadicon.png') no-repeat center left;
                    background-size: contain;
                    position: absolute;
                    top: 50%;
                    transform: translateY(-50%);
                    left: 1rem;
                  }
                  .upload_text1{
                    font-size: 1.2rem;
                    color: #fff;
                    padding-left: 2rem;
                  }
                }
                .box{
                  margin-bottom: 10px;
                  span{
                        font-size: 1.2rem;
                  }
                }
                .closeX{
                  color: #fff;
                  margin-left: 10px;
                  background-color: red;
                  border-radius: 50%;
                  width: 1.5rem;
                  height: 1.5rem;
                  display: inline-block;
                  font-size: 1.2rem;
                  text-align: center;
                  line-height: 1.5rem;
                }
              }
            }
            &:nth-child(6){
              // width: 50%;
              // float: left;
              .radio_pay{
                  // border: 2px solid #999999;
                  height: 3.5rem;
                  line-height: 3.5rem;
                  padding-left: 1rem;
                  font-size: 1.2rem;
                  box-sizing: border-box;
                  width: 70%;
                  border-radius: 8px;

                  label{
                    display: flex;
                    align-items: center;
                    margin-top: -2px;
                    input:not([type="range"]):not([type="color"]){
                      margin-top: 0;
                    }
                  }
              }

            }

          }
          p[name="error"] {
            color: red;
            font-size: 1rem;
          }
          .only_tilte {
            width: 100%;
            .control-label {
              color: #333333;
              font-size: 1.6rem;
              font-weight: 500;
              width: 100%;
              display: block;
              text-align: center;
            }
          }
        }
        .row.pay{
          display: none;
        }
      }

      /deep/ #preview {
        padding: 0;
        >div {
            margin-bottom: 20px;
        }
        .title {
            font-size: 1.4rem;
        }
        .question {
            color: @base_color;
            font-size: 1.2rem;
        }
        .anwer {
            color: #666;
            font-size: 1.2rem;
        }
        p {
            word-break: break-word;
        }
        input[type="button"] {
          font-size: 1.1rem;
          height: 3.2rem;

          &:first-of-type {
            margin-top: 2rem;
            background: @base_color;
            font-size: 1.4rem;
            color:#fff;
            border: 0px;
            width: 48%;
            margin-right: 4%;
            border-radius: 2rem;
          }
          &:last-of-type {
            margin-top: 2rem;
            background: @base_color;
            font-size: 1.4rem;
            color:#fff;
            border: 0px;
            width: 48%;
            border-radius: 2rem;
          }
        }
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
      width: 90%;
      margin: 0 auto;
      margin-top: 2rem;
      /deep/ .swiper-container {
        padding-bottom: 3rem;
        .swiper-pagination-bullet {
          background: #e6e6e6;
          opacity: 1;
        }
        .swiper-pagination-bullet-active {
          background: #3c408e!important;
        }
      }
    }
    .JoinUs {
      width: 90%;
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
    display: inline-block;
    background: #f8f8f8 url("/images/mobile/mobileindex_10.png") no-repeat center bottom;
    padding-top: 0;
    padding-bottom: 6rem;
    background-size: cover;
    .Main {
      width: 90%;
      margin: 0 auto;
      .Facebook {
        text-align: center;
        margin-bottom: 2rem;
        /deep/ img {
          width: 100%;
        }
        /deep/.contactBox{
  width: 100%;
  text-align: center;
  p{
    // padding-top: 1.5rem;
    padding-bottom: 10px;
    font-size: 1.2rem;
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: 'SourceHanSerifCN-Regular';
    color: #333333;
    &:nth-child(1){
      font-size: 1.6rem;
    }
    &:nth-child(3){
      justify-content: left;
      padding-left: 4rem;
    }
    &:nth-child(4){
      justify-content: left;
      padding-left: 4rem;
    }
    &:nth-child(5){
      justify-content: left;
      padding-left: 4rem;
    }
    &:nth-child(6){
      justify-content: left;
      padding-left: 4rem;
    }
    .icon1{
      background: url('/images/pc/PC_index_51.png') no-repeat center center;
      background-size: 100%;
      width: 1.4rem;
      height: 1.4rem;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
          display: block;
    -ms-flex-negative: 0;
    flex-shrink: 0;
    }
    .icon2{
      background: url('/images/pc/PC_index_56.png') no-repeat center center;
      background-size: 100%;
      width: 1.4rem;
      height: 1.4rem;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
          display: block;
    -ms-flex-negative: 0;
    flex-shrink: 0;
    }
    .icon3{
      background: url('/images/pc/PC_index_54.png') no-repeat center center;
      background-size: 100%;
      width: 1.4rem;
      height: 1.4rem;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
          display: block;
    -ms-flex-negative: 0;
    flex-shrink: 0;
    }
    .icon4{
      background: url('/images/pc/PC_index_47.png') no-repeat center center;
      background-size: 100%;
      width: 1.4rem;
      height: 1.4rem;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
          display: block;
    -ms-flex-negative: 0;
    flex-shrink: 0;
    }
    .icon5{
      background: url('/images/pc/PC_index_58.png') no-repeat center center;
      background-size: 100%;
      width: 1.4rem;
      height: 1.4rem;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
          display: block;
    -ms-flex-negative: 0;
    flex-shrink: 0;
    }
  }
        }
      }

    }

  }
  .hkmaps{
    .IG {
        text-align: center;
        /deep/ p iframe{
          display: block;
        }
        /deep/ img {
          width: 100%;
        }
      }
  }
}
</style>
