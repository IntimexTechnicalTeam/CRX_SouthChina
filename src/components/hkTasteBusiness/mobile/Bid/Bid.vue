<template>
  <div class="Container">
    <div class="Warper">
      <div class="Inner">
        <div class="Top">
          <div class="perData">
            <p class="Title">{{ $t("Bid.Id") }}</p>
            <p class="inputBox" v-if="item">
              <input placeholder="" class="textInput" v-model="item.Bider.Code" disabled />
            </p>
          </div>
          <div class="perData" v-if="item">
            <p class="Title">{{ $t("Bid.Email") }}</p>
            <p class="inputBox">
              <input placeholder="" class="textInput" v-model="item.Bider.Email" disabled />
            </p>
          </div>
          <div class="perData" v-if="item">
            <p class="Title">{{ $t("Bid.Name") }}</p>
            <p class="inputBox">
              <input placeholder="" class="textInput" v-model="item.Bider.Name"  disabled />
            </p>
          </div>
          <div class="perData" v-if="item">
            <p class="Title">{{ $t("Bid.Mobile") }}</p>
            <p class="inputBox">
              <input placeholder="" class="textInput" v-model="item.Bider.WhatsApp" disabled />
            </p>
          </div>
        </div>
        <div class="Bottom">
          <p class="NormalTitle">
            <span>{{ $t("Bid.DeadlineDate") }}:</span><span>2023-06-07</span>、
            <span>{{ $t("Bid.QuotationNumber") }}:</span><span>HK123456</span>
          </p>
          <div class="DataForm">
            <el-form
              :model="item"
              :rules="rules"
              :label-position="'right'"
              label-width="120px"
              ref="Bider"
              class="BiderForm"
            >
            <div v-for="(v,index) in item.BidViews " :key="index" class="BidViews">
              <el-form-item :label="$t('Bid.Price')" prop="Price">
                <el-input
                  v-model="v.Price"
                  v-bind:disable="v.IsWin"
                  :placeholder="$t('Bid.Price')"
                ></el-input>
              </el-form-item>
              <el-form-item :label="$t('Bid.Remark')" prop="Remark">
                <el-input
                  v-model="v.Remark"
                  :placeholder="$t('Bid.Remark')"
                ></el-input>
              </el-form-item>
              <el-form-item
                prop="filePath"
                class="fileOne"
                :label="$t('Bid.BiddingDocument')"
              >
                <el-upload
                  class="upload-demoB"
                  :action="uploadUrl"
                  :before-upload="beforeAvatarUpload"
                  :on-success="uploadSucceed"
                  :on-remove="handleRemove"
                  :limit="1"
                  :on-exceed="handleExceed"
                  :headers="{ Authorization: 'bearer ' + token }"
                >
                    <img src="/images/pc/upload.png">
                </el-upload>
              </el-form-item>
              </div>
            </el-form>
          </div>
          <div class="bottomBtn">
            <a href="javascript:;" @click="SubmitForm('Bider')">{{
              $t("Bid.Signin")
            }}</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
import lang from '@/lang';
import storage from '@/sdk/common/Storage';
import Cookie from 'js-cookie';
import { ElForm } from 'element-ui/types/form';
@Component({
  components: {}
})
export default class Bid extends Vue {
  item:any={
  };
  BidViews:any=[];
  uploadUrl: string = '';
  showUploadBtn: boolean = true;
  fileListAPath: string = '';
  IsMobile: boolean = false;
  checkEmail = (rule, value, callback) => {
    const mailReg = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/;
    if (!value) {
      return callback(new Error(this.$t('Bid.EnterEmail') as string));
    }
    setTimeout(() => {
      if (mailReg.test(value)) {
        callback();
      } else {
        callback(new Error(this.$t('Bid.CorrectEmail') as string));
      }
    }, 100);
  };
  get token() {
    return Cookie.get('access_token');
  }
  get rules() {
    return {
      Name: [
        {
          required: true,
          message: this.$t('Bid.EnterCompanyName'),
          trigger: 'blur'
        }
      ],
      ContactPerson: [
        {
          required: true,
          message: this.$t('Bid.EnterContactPerson'),
          trigger: 'blur'
        }
      ],
      Email: [{ validator: this.checkEmail, trigger: 'blur', required: true }],
      Phone: [
        {
          required: true,
          message: this.$t('Bid.EnterPhone'),
          trigger: 'blur'
        },
        {
          validator: function (rule, value, callback) {
            /* eslint-disable */
            var mobile = /^(\+)?(\d{0,4}\-?)?\d{7,11}$/;
            if (mobile.test(value) === false) {
              var t =
                lang.messages[storage.get("locale")].Input["phoneincorrect"];
              callback(new Error(t));
            } else {
              callback();
            }
          },
          trigger: "blur",
        },
      ],
      Mobile: [
        {
          required: true,
          message: this.$t("Bid.EnterPhone"),
          trigger: "blur",
        },
        {
          validator: function (rule, value, callback) {
            /* eslint-disable */
            var mobile = /^(\+)?(\d{0,4}\-?)?\d{7,11}$/;
            if (mobile.test(value) === false) {
              var t =
                lang.messages[storage.get("locale")].Input["phoneincorrect"];
              callback(new Error(t));
            } else {
              callback();
            }
          },
          trigger: "blur",
        },
      ],
      Address: [
        {
          required: true,
          message: this.$t("Bid.EnterAddress"),
          trigger: "blur",
        },
      ],
    };
  }
  get currentlang() {
    return this.$Storage.get("locale");
  }
  get id () {
    return this.$route.params.id;
  }
  // 超出文件數目
  handleExceed(files, fileList) {
    Vue.prototype.$Confirm(
      lang.t("BoAForm.exceed"),
      lang.t("BoAForm.exceedText")
    );
    // this.$message.warning(`当前限制选择 1个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
  }
  async beforeAvatarUpload(file) {
    this.uploadUrl = await this.$Api.comments.uploadFile();
    return true;
  }
  uploadSucceed(res, file) {
    console.log(res.ReturnValue[0],'res.ReturnValue[0]');
    (this as any).item.BidViews.FileLink = res.ReturnValue[0].Path;
    this.fileListAPath = res.ReturnValue[0].Path;
    this.showUploadBtn = false;
  }
  handleRemove(file, fileList) {
    (this as any).item.BidViews.FileLink  = "";
    this.$Api.comments.deleteUploadFile(this.fileListAPath);
  }
  SubmitForm(Bider) {
    let that = this;
    (that.$refs.Bider as ElForm).validate((valid) => {
      if (valid) {
        this.$Api.enquiry.SaveInviteBidList(this.item).then(result => {
            console.log(result,'result');
        });
      } else {
        console.log("error submit!!");
        return false;
      }
    });
  }
  GetInviteBidListByBider () {
    this.$Api.enquiry.GetInviteBidListByBider(this.id).then(result => {
        console.log(result,'result');
        this.item= result;
    });
  };
  created() {
    this.GetInviteBidListByBider();
  }
  mounted() {}
}
</script>
<style scoped lang="less">
.Container {
  width: 100%;
  display: inline-block;
  background: url("/images/pc/formBg.jpg") no-repeat center center;
  background-size: cover;
  min-height: 100vh;
  .Warper {
    width: 950px;
    margin: 0 auto;
    margin-top: 100px;
    background: #fff;
    box-shadow: 2px 2px 5px #ddd;
    border-radius: 5px;
    .Inner {
      padding: 40px;
      .Top {
        width: 100%;
        display: inline-block;
        .perData {
          width: 48%;
          float: left;
          margin-right: 4%;
          margin-bottom: 20px;
          .Title {
            font-size: 16px;
            color: #999;
            margin-bottom: 10px;
          }
          .inputBox {
            width: 100%;
            input {
              width: 100%;
              height: 40px;
              line-height: 40px;
              text-indent: 10px;
              padding: 0px;
              margin: 0px;
              box-sizing: border-box;
            }
          }
          &:nth-child(2n) {
            margin-right: 0px !important;
          }
        }
      }
      .Bottom {
        width: 100%;
        display: inline-block;
        margin-top: 20px;
        .NormalTitle {
          padding: 0px;
          margin: 0px;
          display: flex;
          align-items: center;
          margin-bottom: 20px;
          span {
            font-size: 20px;
            color: #7fb37f;
          }
          &::before {
            content: "";
            width: 6px;
            height: 32px;
            line-height: 32px;
            display: inline-block;
            margin-right: 10px;
            background: #81b581;
          }
        }
        .DataForm {
          width: 100%;
          display: inline-block;
          .addPath {
            width: 100%!important;
            text-align: center;
            display: inline-block;
            img {
                width: 50px;
            }
          }
          .BidViews {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;  
          }
          /deep/ .el-form {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            .el-form-item {
              width: 25%;
              display: flex;
              flex-wrap: wrap;
              .el-upload {
                width: 100%!important;
                text-align: center;
                display: flex;
                box-sizing: border-box;
                padding: 0;
                margin: 0;
                height: 40px;
                line-height: 40px;
                align-items: center;
                justify-content: center;
                border-radius: 3px;
                border: 1px solid #eee;
                img {
                    width: 25px;
                }
              }
              .el-upload-list {
                position: absolute;
              }
              .el-form-item__content {
                margin-left: 0px !important;
                width: 100%;
              }
              .el-form-item__label {
                width: 100% !important;
                text-align: left !important;
              }
              &:nth-child(2) {
                width: 40% !important;
              }
            }
          }
        }
      }
      .bottomBtn {
        a {
          background: #62a262;
          display: flex;
          justify-content: center;
          width: 50%;
          margin: 0 auto;
          height: 40px;
          line-height: 40px;
          text-align: center;
          color: #fff;
          font-size: 20px;
          margin-top: 50px;
          &:hover {
            background: #333;
          }
        }
      }
    }
  }
}
</style>
