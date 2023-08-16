<template>
  <div class="Container">
    <div class="Warper">
      <div class="Logo"><img src="/images/pc/titleLogo.png" /></div>
      <div class="Regtitle">{{ $t("Bid.Supplierregistrationform") }}</div>
      <div class="PerForm">
        <el-form
          :model="registerForm"
          :rules="rules"
          :label-position="'right'"
          label-width="180px"
          ref="Bider"
          class="BiderForm"
        >
        <el-form-item :label="$t('Enquiry.registrationType')" >
            <el-select v-model="selectTypes" placeholder="$t('Enquiry.registrationType')" style="width:100%">
            <el-option
              v-for="item in types"
              :key="item.value"
              :label="item.name"
              :value="item.value">
            </el-option>
          </el-select>
          </el-form-item>
          <el-form-item :label="$t('Register.UserFirstName')" prop="firstName">
            <el-input
              v-model="registerForm.firstName"
              :placeholder="$t('Register.UserFirstName')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('Register.UserLastName')" prop="lastName">
            <el-input
              v-model="registerForm.lastName"
              :placeholder="$t('Register.UserLastName')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('Register.UserRegPassword')" prop="password">
            <el-input
              v-model="registerForm.password"
              :placeholder="$t('Register.UserRegPassword')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('Register.UserConfirmPassword')" prop="confirmPassword">
            <el-input
              v-model="registerForm.confirmPassword"
              :placeholder="$t('Register.UserConfirmPassword')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('DeliveryAddress.Mobile')" prop="Mobile">
            <el-input
              v-model="registerForm.Mobile"
              :placeholder="$t('DeliveryAddress.Mobile')"
            ></el-input>
          </el-form-item>
           <el-form-item :label="$t('Register.UserEmail')" prop="email">
            <el-input
              v-model="registerForm.email"
              :placeholder="$t('Register.UserEmail')"
            ></el-input>
          </el-form-item>
            <div class="register_half" v-if="selectTypes===2">
                <el-form-item :label="$t('Enquiry.Company')" prop="Company">
                  <el-input
                    v-model="registerForm.Company"
                    :placeholder="$t('Enquiry.Company')"
                  ></el-input>
                </el-form-item>
                <el-form-item :label="$t('Enquiry.Title')" prop="JobTitle">
                  <el-input
                    v-model="registerForm.JobTitle"
                    :placeholder="$t('Enquiry.Title')"
                  ></el-input>
                </el-form-item>
                <el-form-item :label="$t('Enquiry.Address')" prop="email">
                  <el-input
                    v-model="registerForm.CompanyAddress"
                    :placeholder="$t('Enquiry.Address')"
                  ></el-input>
                </el-form-item>
            </div>
          <div class="bottomBtn">
            <div class="left"></div>
            <div class="right">
                <el-checkbox v-model="terms" style="margin: 10px 0 0 0"><label @click="toURL('/CMS/content/30343')">{{$t('Register.RegisterAgree')}}</label></el-checkbox>
            </div>
          </div>
          <div class="bottomBtn">
            <div class="left"></div>
            <div class="right">
              <a href="javascript:;" @click="register">{{
                $t("Bid.Signin")
              }}</a>
            </div>
          </div>
        </el-form>
      </div>
    </div>
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
import lang from '@/lang';
import storage from '@/sdk/common/Storage';
import { ElForm } from 'element-ui/types/form';
import InsForm from '@/components/base/pc/InsForm.vue';
@Component({
  components: {}
})
export default class InsertBider extends Vue {
  private terms: boolean = true;
  selectTypes: number = 1;
  lang:string[] = ['E', 'C', 'S'];
  private types: any[] = []
  private registerForm = {
    email: '',
    password: '',
    firstName: '',
    lastName: '',
    confirmPassword: '',
    Language: '',
    Mobile: '',
    Company: '',
    CompanyAddress: '',
    JobTitle: ''
  };
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
  get rules() {
    return {
      firstName: [
        {
          required: true,
          message: this.$t('Bid.EnterCompanyName'),
          trigger: 'blur'
        }
      ],
      lastName: [
        {
          required: true,
          message: this.$t('Bid.EnterContactPerson'),
          trigger: 'blur'
        }
      ],
      email: [{ validator: this.checkEmail, trigger: 'blur', required: true }],
      Mobile: [
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
      password: [
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
  register() {
    let _this = this;
    let l = this.$Storage.get("locale");
    this.lang.forEach((element, index) => {
      if (l === element) this.registerForm.Language = "" + index;
    });
    (_this.$refs.Bider as ElForm).validate(valid => {
      if (valid && this.terms) {
        this.$Api.member.register(this.registerForm).then((result) => {
          console.log(this.registerForm, "this.registerForm");
          if (result.Succeeded) {
            this.$Api.member
              .login(this.registerForm.email, this.registerForm.password, true)
              .then(
                function (response) {
                  _this.$store.dispatch("doLogin");
                  return _this.$route.query && _this.$route.query.returnurl
                    ? _this.$route.query.returnurl
                    : undefined;
                },
                function (response) {
                  _this.$message({
                    message: response.Message,
                    type: "error",
                    customClass: "messageBoxMobile",
                  });
                }
              )
              .then((url) => {
                this.$Api.member.getProfile().then(
                  function (data) {
                    if (data) {
                      // _this.registerForm = data;
                      _this.$store.dispatch(
                        "setUser",
                        (data.FirstName + " " + data.LastName).toUpperCase()
                      );
                      _this.$i18n.locale = _this.lang[data.Language];
                      _this.$store.dispatch(
                        "setLang",
                        _this.lang[data.Language]
                      );
                      _this.$Storage.set("locale", _this.lang[data.Language]);
                      _this.$store.dispatch("setMemberInfo", data);
                      if (url) {
                        window.location.href = _this.$route.query
                          .returnurl as string;
                      } else {
                        window.location.href = "/account/memberInfo";
                      }
                    } else {
                      _this.$store.dispatch("Logout");
                    }
                  },
                  function (data) {
                    _this.$message({
                      message: data,
                      type: "error",
                      customClass: "messageBoxMobile",
                    });
                  }
                );
              });
          } else {
            this.$message({
              message: result.Message,
              type: "error",
              customClass: "messageBoxMobile",
            });
          }
        });
      } else {
        console.log("error submit!!");
        return false;
      }
    });
  }
  created() {
          this.types = [
        {
          name: this.$t('Enquiry.Individual'),
          value: 1
        },
        {
          name: this.$t('Enquiry.Enterprise'),
          value: 2
        }
      ];
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
  padding-bottom: 100px;
  .Warper {
    width: 500px;
    margin: 0 auto;
    margin-top: 100px;
    .Logo {
      width: 100%;
      text-align: center;
      img {
        width: 300px;
      }
    }
    .Regtitle {
      width: 100%;
      display: inline-block;
      margin-top: 30px;
      margin-bottom: 30px;
      color: #62a262;
      font-size: 26px;
      text-align: center;
    }
    .bottomBtn {
      width: 100%;
      display: flex;
      justify-content: space-between;
      margin-bottom: 20px;
      .left {
        width: 120px;
      }
      .right {
        width: calc(100% - 120px);
        a {
          background: #62a262;
          display: inline-block;
          width: 100%;
          height: 40px;
          line-height: 40px;
          text-align: center;
          color: #fff;
          font-size: 20px;
          &:hover {
            background: #333;
          }
        }
      }
    }
    /deep/ .el-form {
      .el-form-item__label {
        color: #999999;
        font-size: 16px;
      }
    }
  }
}
</style>
