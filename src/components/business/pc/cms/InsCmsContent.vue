<template>
  <div id="container" class="PcContact" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <!-- 联络我们页面 -->
    <div class="CmsContact" v-if="content.Key=='contactus'">
      <!-- 表单信息 -->
        <div class="Banner" v-if="content.Cover"><img :src="content.Cover"></div>
        <div class="position">

          <div class="left">
            <div class="NormalTitle">
            <!-- <i class="left"></i> -->
            <span class="center">{{ content.Title }}</span>
            <!-- <i class="right"></i> -->
          </div>
            <p v-html="content.Body" class="text"></p>
          </div>
          <div class="right">
            <div class="ContactMain contactContainer">
              <div class="FormMain">
                <div v-html="htmlString" class="to_vertical" id="content"></div>
                <div id="preview" style="display: none"></div>
              </div>
              <!-- <p v-html="MapInfo.Content" class="MapInfo"></p> -->
            </div>
          </div>
            <!-- <div class="Location"><span class="HomeText">{{$t('Message.Home')}} > </span><span class="ColorText">{{ content.Title }}</span></div> -->

        </div>
    </div>
    <div class="Onlineaudit_box" v-else-if="fromcontent.Key=='Onlineaudit'">
      <div class="Banner" v-if="catimg">
        <img :src="catimg">
      </div>
      <div class="position">
        <!-- <div class="NormalTitle">
          <span class="center">{{ content.Title }}</span>
        </div> -->
        <div class="Onlineaudit_Main">
          <RNPForm :formKey='contentKey'/>
        </div>
      </div>
    </div>
    <!-- 其他页面 -->
    <div class="CmsNormal" v-else>
      <div class="Banner" v-if="content.Cover">
        <img :src="content.Cover">
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
      </div>

    </div>
    <!-- <div class="quote">
      <router-link to="#">
        <span>{{$t('home.Quote')}}</span>
      </router-link>
    </div> -->
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component({
  components: {
    RNPForm: () => import('@/views/pc/InsRegAndPay.vue')
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
  currentPage:number=1;
  pageSize:number=12;
  totalRecord:number=0;
  private SortOrder: string = 'desc';
  private SortName: string = 'CreateDate';
  catimg: string = '';
  fromcontent: string = '';
  contentKey: string = '';
  get currentlang () {
    return this.$Storage.get('locale');
  }
  get id () {
    return this.$route.params.id ? this.$route.params.id : '';
  }

  getContent () {
    this.$Api.cms.getContentByDevice({ Key: this.id, ContentId: this.id, IsMobile: false }).then(result => {
      this.content = result.CMS;
      if (result.CMS.Title) document.title = result.CMS.Title;
      console.log(result, 'getContent');
      this.getFromContentByCatId(result.CMS.CatId);
      this.contentKey = result.CMS.Key;
    });
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
  getCategoryByDevice () {
      this.$Api.cms.getCategoryByDevice({ CatId: '40112', IsMobile: false }).then(async (result) => {
        console.log(result, 'result');
        this.MapInfo = result;
    });
  }
  getFromContentByCatId (cateId) {
    this.$Api.cms.getCategoryByDevice({ CatId: cateId, IsMobile: false }).then((result) => {
        console.log(result, 'ddd');
        if (result) {
            this.fromcontent = result;
            this.catimg = result.ImagePath;
        }
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
          $(item).css('width', '48%');
        }
      });
      $('.contactContainer.contactContainerM .form-group').each((index, item) => {
        if ($(item).find('.text').length > 0 || $(item).find('.email').length > 0) {
          $(item).css('width', '100%');
        }
      });
    });
  }
  getFileNameToText() {
    // 获取上传控件domvar fileObj = this.$refs['upload'];
    // 获取文件名var fileName = fileObj.files[0].name;
    // 获取文件路径var filePath = fileObj.value;
    // 将文件名载入文本框this.$refs['filePath'].value = fileName;console.log(fileName, 'fileName');console.log(filePath, 'filePath');

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
.PcContact {
  width: 100%;
  display: inline-block;
  // padding-bottom: 10rem;
  .Banner {
    width: 97%;
    min-width: 1200px;
    margin: 0 auto;
    img {
      width: 100%;
      display: block;
      min-height: calc(100vh - 204px);
      // min-height: 78vh;
      vertical-align: top;
      display: block;
      -o-object-fit: cover;
      object-fit: cover;
      -o-object-position: left;
      object-position: left;
    }
  }
  .Location {
    width: 1200px;
    margin: 0 auto;
    margin-bottom: 1rem;
    .HomeText {
      font-size: 20px;
      color:#999999;
    }
    .ColorText {
      font-size: 20px;
      color: @base_color;
    }
  }
    .CmsContact {
      width: 100%;
      margin: 0 auto;
      position: relative;
      .position{
        width: 88%;
        left: 0;
        padding-left: 10%;
        .left{
          width: 50%;
          float: left;
        }
        .right{
          width: 50%;
          float: right;
        }
      }
      /deep/.contactBox{
  width: 100%;
  text-align: left;
  p{
    // padding-top: 1.5rem;
    padding-bottom: 10px;
    font-size: 20px;
    display: flex;
    align-items: center;
    justify-content: left;
    font-family: 'SourceHanSerifCN-Regular';
    color: #333333;
    &:nth-child(1){
      font-size: 24px;
    }
    // &:nth-child(5){
    //   padding-left: 120px;
    // }
    // &:nth-child(6){
    //   padding-left: 98px;
    // }
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
      .ContactMain {
        width: 100%;
        display: flex;
        justify-content: space-between;
        // padding-top: 2rem;
      }
      .FormMain {
        width: 80%;
        // padding-bottom: 3rem;
        position: relative;
        /deep/ #content {
          .btn.save {
            border: 0;
            // width: 100%;
            background: @base_color;
            border-radius: 30px;
            font-size: 20px;
            height: 60px;
            margin: 0;
            font-weight: 500 !important;
            color: #fff;
            font-family: 'SourceHanSerifCN-Regular';
            padding: 0 40px;
          }

          #Anwers {
                  display: flex;
                  flex-wrap: wrap;
                  justify-content: space-between;
                  margin:0!important;
                  p[name="error"]{
                    // margin:3px 0!important;
                    margin-bottom: 5px;
                    padding-left:5px;
                    color: red;
                    font-size: 14px;
                  }
                  .form-group {
                    padding: 0;
                    border: 0;
                    width: 100%;
                    // margin-bottom:4%!important;
                    background:transparent!important;
                    .control-label{
                      font-size:18px;
                      color:#000!important;
                      font-family: 'SourceHanSerifCN-Regular';
                      font-weight: normal;
                      margin-bottom: 20px;
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
                      width: 100%;
                      padding: 0;
                      margin: 0;
                      margin-bottom: 26px;
                      // height: 40px;
                      input[type="text"],[type="email"]{
                        // height:60px!important;
                        box-sizing: border-box;
                        padding:0 0 0 15px!important;
                        border:0!important;
                       font-family: 'SourceHanSerifCN-Regular';
                        font-size: 18px;
                        background:#fff;
                        height: 50px;
                        outline: none;
                        width: 100%;
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
                }
        }

        /deep/ #preview {
          padding: 0;
          >div {
              margin-bottom: 20px;
              width: 49%;
              float: left;
              &:nth-child(2n+1){
                float: right;
                margin-left: 2%;
              }
          }
          .title {
              font-size: 1.4rem;
              width: 100%;
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
        width: 58%;
        /deep/ img {
          width: 100%;
        }
      }
    }
  .CmsContent {
    width: 1200px;
    margin: 0 auto;
    .text {
      width: 500px;

      /deep/ p{
        font-size: 24px;
        line-height: 32px;
        color: #333333;
        text-align: justify;
        font-family: 'SourceHanSerifCN-Regular';
      }
      /deep/ img {
        width: 100%;
        display: block;
      }
      /deep/ .AboutMain {
        width: 100%;
        display: flex;
        justify-content: space-between;
        .left {
          width: 48%;
          p{
            font-size: 18px;
            line-height: 30px;
            color: #666666;
          }
        }
        .right{
          width: 48%;
        }
      }
    }
  }
  .position{
      position: absolute;
      left: 10%;
      top: 6%;
    }
  .CmsNormal{
    position: relative;

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
        width: 234px;
        height: 2px;
        background-color: #3c408e;
        position: absolute;
        bottom: -17px;
        left: 16px;
        transform:translateX(0);
      }
    }
  }
  // .quote{
  //   position: absolute;
  //   left: 1.5%;
  //   bottom: 0;
  //   width: 250px;
  //   height: 80px;
  //   background: url(/images/pc/arrow.png) 87% center #3c408e no-repeat;
  //   z-index: 100;
  //   span{
  //     display: block;
  //     width:78%;
  //     color: #fff;
  //     font-size: 24px;
  //     line-height: 80px;
  //     text-align: center;
  //     letter-spacing: 0.05em;
  //     font-family: 'SourceHanSerifCN-Regular';
  //   }
  // }
}
.Onlineaudit_box{
  .Onlineaudit_Main{
    /deep/ .NomralBg{
      padding-bottom: 0;
      .RNPForm.default{
        background-color: #ffffff;
        padding: 38px 78px;
        width: 800px;
        // height: 530px;
        border: 1px solid #999999;
        background-color: #ffffff;
        box-sizing: border-box;
        padding-left: 0;
        .FormMain{
        width: 100%;
        margin: 0 auto;
        // margin-top: 28px;
        // box-shadow: 0 6px 10px #d9d9d9;
        // padding: 30px 80px;
        box-sizing: border-box;
            min-width: 100%;
        #content {
          position: relative;
          .form-group{
            text-align: right;
          }
          .row.BottomBorder{
            margin: 0;
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
                    margin: 0;
                    margin-left: 180px;
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
                        margin-left: 68px;
                        // margin-top: 75px;
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
                        width: 72%;
                        height: 50px;
                        line-height: 50px;
                        float: left;
                      }
                      p[name="error"]{
                        margin-left: 52px;
                      }
                    }

                    .control-label{
                      font-size:18px;
                      color:#000!important;
                      font-family: 'SourceHanSerifCN-Regular';
                      font-weight: normal;
                      // margin-bottom: 20px;
                      width: 158px;
                      text-align: right;
                      padding-right: 20px;
                      float: left;
                      display: flex;
                      justify-content: right;
                      align-items: center;
                      height: 50px;
                      margin-bottom: 0;
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
                      width: 542px;
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
                        border-radius: 5px;
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
  }
}
.ENG{
  .CmsContact{
    /deep/ .contactBox {
      width: 90%;
      p .icon4{
        display: block;
        flex-shrink:0;
      }
    }
  }
  .CmsContact .FormMain /deep/ #content #Anwers .form-group fieldset.checkbox .option{
    width: 50%;
    label{
      display: flex;
    align-items: center;
    }
  }
  // .CmsContact .FormMain/deep/ #content #Anwers .form-group{
  //   .control-label{
  //     font-family: Arial !important;
  //   }
  //   fieldset{
  //     input[type="text"],[type="email"], textarea{
  //       font-family: Arial !important;
  //     }
  //   }
  // }
  // .CmsContact/deep/ .contactBox p{
  //   font-family: Arial !important;
  // }
  // .NormalTitle .center{
  //   font-family: Arial !important;
  // }
  // .CmsContent .text/deep/ p{
  //   font-family: Arial !important;
  // }
}
</style>
