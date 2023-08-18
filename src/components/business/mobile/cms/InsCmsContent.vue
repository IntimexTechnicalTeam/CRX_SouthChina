<template>
  <div id="container" class="MobileContact" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <!-- 联络我们页面 -->
    <div class="CmsContact CmsNormal" v-if="content.Key=='contactus'">
      <!-- 表单信息 -->
        <div class="Banner" :style="'background: #eeeeee url('+content.Cover+') no-repeat right bottom;'">
          <!-- <img :src="content.Cover"> -->
        </div>
        <div class="position">
          <div class="NormalTitle">
            <!-- <i class="left"></i> -->
            <span class="center">{{ content.Title }}</span>
            <!-- <i class="right"></i> -->
          </div>
            <!-- <div class="Location"><span class="HomeText">{{$t('Message.Home')}} > </span><span class="ColorText">{{ content.Title }}</span></div> -->
          <div class="CmsContent">
            <p v-html="content.Body" class="text"></p>
          </div>
          <div class="FormMain contactContainer">
            <div v-html="htmlString" class="to_vertical" id="content"></div>
            <div id="preview" style="display: none"></div>
          </div>
          <p v-html="MapInfo.Content" class="MapInfo"></p>
        </div>

    </div>
    <div class="Onlineaudit_box CmsNormal" v-else-if="fromcontent.Key=='Onlineaudit'">
      <div class="Banner" v-if="catimg">
        <img :src="catimg">
      </div>
      <div class="position">
        <div id="Onlineaudit_Main">
          <RNPForm :formKey='contentKey'/>
        </div>
      </div>
    </div>
    <!-- 其他页面 -->
    <div class="CmsNormal" v-else>
      <div class="Banner"><img :src="content.Cover"></div>
      <div class="position">
        <div class="NormalTitle">
          <!-- <i class="left"></i> -->
          <span class="center">{{ content.Title }}</span>
          <!-- <i class="right"></i> -->
        </div>
        <!-- <div class="Location"><span class="HomeText">{{$t('Message.Home')}} > </span><span class="ColorText">{{ content.Title }}</span></div> -->
        <div class="CmsContent">
          <p v-html="content.Body" class="text"></p>
        </div>
      </div>

    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component({
  components: {
    PkcmsBanner: () => import('@/components/hkTasteBusiness/mobile/cms/PkcmsBanner.vue'),
    RNPForm: () => import('@/views/mobile/InsRegAndPay.vue')
  }
})
export default class InsCmsContent extends Vue {
  content: any[] = [];
  FormContent:any='';
  IsPay:boolean= false;
  IsLogin:boolean=false;
  IsMobile:boolean=true;
  private htmlString: string = '';
  Signer: any = null;
  FormTitle:string='';
  private waiting: boolean = true;
  TitleName:string='';
  MapInfo:string='';
  catimg: string = '';
  fromcontent: string = '';
  contentKey: string = '';
  get currentlang () {
    return this.$Storage.get('locale');
  }
  get id () {
    return this.$route.params.id ? this.$route.params.id : '';
  }
  getForm () {
    this.$Api.regAndPay.getHtml('ContactUs', this.lang, false).then(result => {
      this.htmlString = result.HtmlString;
      this.FormTitle = result.Title;
      this.$nextTick(() => {
        if (document.querySelectorAll('#Sign').length > 0) {
          this.Signer = new intimex.CanvasSigner('#NewSignCanvas', '#Signature', {
            color: '#58B63A',
            width: 5
          });
          this.Signer.initCanvas();
          window['Signer'] = this.Signer;
        }
      });
    });
  }
  getContent () {
    this.$Api.cms.getContentByDevice({ Key: this.id, ContentId: this.id, IsMobile: true }).then(result => {
      this.content = result.CMS;
      if (result.CMS.Title) document.title = result.CMS.Title;
      this.getFromContentByCatId(result.CMS.CatId);
      this.contentKey = result.CMS.Key;
    });
  }
  getFromContentByCatId (cateId) {
    this.$Api.cms.getCategoryByDevice({ CatId: cateId, IsMobile: true }).then((result) => {
        console.log(result, 'ddd');
        if (result) {
            this.fromcontent = result;
            this.catimg = result.ImagePath;
        }
      });
  }
    getCategoryByDevice () {
      this.$Api.cms.getCategoryByDevice({ CatId: '40112', IsMobile: true }).then(async (result) => {
        console.log(result, 'result');
        this.MapInfo = result;
    });
  }
  get lang () {
    return this.$Storage.get('locale');
  }
  get queryLang () {
    return this.$route.query.Lang || '';
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
  updated () {
    this.$nextTick(() => {
      $('.contactContainer .control-label').each((index, item) => {
        if ($(item).find('.control-label-remark').length > 0 && $(item).next().find('.email').length === 0) {
          $(item).show();
        } else {
          $(item).hide();
        }
      });
      $('.contactContainer .form-group').each((index, item) => {
        if ($(item).find('.text').length > 0 || $(item).find('.email').length > 0) {
          $(item).css('width', '100%');
        }
      });
      $('.contactContainer.contactContainerM .form-group').each((index, item) => {
        if ($(item).find('.text').length > 0 || $(item).find('.email').length > 0) {
          $(item).css('width', '100%');
        }
      });
    });
  }
  created () {
    this.getContent();
    this.Regnay();
    this.getCategoryByDevice();
  }
  mounted () {
    window['regAndPay'] = this.$Api.regAndPay;
    window['router'] = this.$router;
    window['Elalert'] = this.$alert;
  }
  @Watch('$route', { deep: true })
  onIdChange () {
    this.getContent();
  }
}
</script>
<style scoped lang="less">
.MobileContact {
  width: 100%;
  display: block;
  // padding-bottom: 6rem;
  position: relative;
  .Banner {
    width: 100%;
    display: block;
    img {
      width: 100%;
      display: block;
    }
  }
  .Location {
    width: 90%;
    margin: 0 auto;
    margin-bottom: 1rem;
    .HomeText {
      font-size: 1.4rem;
      color:#999999;
    }
    .ColorText {
      font-size: 1.4rem;
      color: @base_color;
    }
  }
    .CmsContact {
      .Banner{
        width: 100%;
        margin: 1.5rem auto;
        margin-bottom: 0;
        display: flex;
        flex-wrap: wrap;
        position: relative;
        background-position: bottom !important;
        background-size: contain !important;
        background-color: #eeeeee;
        padding-top: 3rem;
        padding-bottom: 50rem;
        height: 100vh;
      }
      .FormMain {
        width: 90%;
        margin: 0 auto;
        padding-bottom: 3rem;
        position: relative;
        padding-top: 1rem;
        /deep/ #content {
          .btn.save {
            border: 0;
            padding: 1rem 3rem;
            background: #3c408e;
            font-size: 1.4rem;
            line-height: 2rem;
            /* height: 3rem; */
            margin: 0;
            font-weight: 500 !important;
            color: #fff;
            font-family: 'SourceHanSerifCN-Regular';
            border-radius: 2.5rem;
          }

          #Anwers {
            display: flex;
            flex-wrap: wrap;
            .form-group {
              padding: 0;
              border: 0;
              width: 100%;
              margin-bottom: 0;
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
                margin-bottom: 1rem !important;
                color: #000000;
                font-size: 1.4rem;
                font-weight: 500;
                display: none;
                span {
                  color: @base_color!important;
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
              .control-label-remark {
                display: none;
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
                margin-bottom: 2rem;
                &.text {
                  input[type="text"] {
                    border: 1px solid #e6e6e6;
                    height: 4rem;
                    line-height: 4rem;
                    padding-left: 1rem;
                    font-size: 1.4rem;
                    box-sizing: border-box;
                    width: 100%;
                    outline: none;
                    &:focus {
                      border: 1px solid @base_color;
                    }
                  }
                }

                &.email {
                  input[type="email"] {
                    border: 1px solid #e6e6e6;
                    height: 4rem;
                    line-height: 4rem;
                    padding-left: 1rem;
                    font-size: 1.4rem;
                    box-sizing: border-box;
                    width: 100%;
                    outline: none;
                    &:focus {
                      border: 1px solid @base_color;
                    }
                  }
                }

                &.textarea {
                  textarea {
                      min-height: 12rem;
                      border: 1px solid #e6e6e6;
                      padding: 1rem;
                      font-size: 1.4rem;
                      box-sizing: border-box;
                      width: 100%;
                      outline: none;
                      color: rgba(102,102,102,1)!important;
                      &:focus {
                      border: 1px solid @base_color;
                      }
                  }
                }
                &.checkbox{
                        margin-bottom: 1rem;
                        input{
                          background:#fff;
                        }
                        .option{
                          width:50%;
                          float:left;
                          margin-bottom: 10px;
                          label{
                            span{
                              font-size:1.4rem;
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
            p[name="error"] {
              color: @base_color;
            }
            .only_tilte {
              .control-label {
                color: @base_color;
                font-size: 20px;
                font-weight: 500;
              }
            }
          }
        }

        /deep/ #preview {
          padding: 0;
          margin-top: 2rem;
          >div {
              margin-bottom: 2rem;
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
              p{
                font-size: 1.2rem;
              }
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
            }
            &:last-of-type {
              margin-top: 2rem;
              background: @base_color;
              font-size: 1.4rem;
              color:#fff;
              border: 0px;
              width: 48%;
            }
          }
        }
      }
      .MapInfo {
        /deep/ img {
          width: 100%;
        }
      }
    }
  .CmsContent {
    width: 90%;
    margin: 0 auto;
    .text {
      /deep/ p{
        font-size: 1.4rem;
        line-height: 2.2rem;
        font-family: 'SourceHanSerifCN-Regular';
      }
      /deep/ img {
        width: 100%;
        display: block;
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
        width: 18rem;
        height: 2px;
        background-color: #3c408e;
        position: absolute;
        bottom: -13px;
        left: 1rem;
        transform:translateX(0);
        background-image: none;
      }
    }
  }
}
.CmsNormal{
  width: 94%;
  margin: 1rem auto;
  margin-bottom: 0;
  position: relative;
  .position{
    position: absolute;
    top: 3rem;
    left: 0;
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
    justify-content: left;
    font-family: 'SourceHanSerifCN-Regular';
    color: #333333;
    text-align: left;
    font-weight: bold;
    &:nth-child(1){
      font-size: 1.6rem;
    }
    &:nth-child(2){
      align-items: baseline;
    }
    // &:nth-child(4){
    //   justify-content: left;
    //   padding-left: 4rem;
    // }
    // &:nth-child(5){
    //   justify-content: left;
    //   padding-left: 4rem;
    // }
    // &:nth-child(6){
    //   justify-content: left;
    //   padding-left: 4rem;
    // }
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
.Onlineaudit_box{
  width: 90%;
  margin: 1.5rem auto;
  margin-bottom: 0;
  .position{
    top: 10rem;
  }
  .Banner{
    img{
      width: 100%;
      display: block;
    }
  }
  #Onlineaudit_Main{
    width: 100%;
      padding: 0 1rem;
      position: relative;
      box-sizing: border-box;

      /deep/.RNPForm.default {
        background-color: #fff;
        .FormMain{
          width: 93%;
        }
        #content {
          .row.BottomBorder{
            display: none;
          }
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
            &:nth-child(5){
              .control-label{
                // display: none;
                opacity: 0;
              }
            }
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
              padding-right: 0;
            }
          }
        }
        .row.pay{
          display: none;
        }
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
.ENG{
  .CmsContact .FormMain/deep/ #content #Anwers .form-group fieldset.textarea textarea{
    font-family: Arial;
  }
  .CmsContact .FormMain /deep/ #content #Anwers .form-group fieldset.checkbox .option{
    width: 100%;
    label{
      display: flex;
    align-items: center;
    }
  }

}
</style>
