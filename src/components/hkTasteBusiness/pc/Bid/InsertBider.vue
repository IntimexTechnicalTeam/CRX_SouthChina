<template>
  <div class="Container">
    <div class="Warper">
      <!-- <div class="Logo"><img src="/images/pc/titleLogo.png"></div> -->
      <div class="Regtitle">{{$t('Bid.Supplierregistrationform')}}</div>
      <div class="PerForm">
        <el-form
          :model="view.Bider"
          :rules="rules"
          :label-position="'right'"
          label-width="180px"
          ref="Bider"
          class="BiderForm"
        >
          <el-form-item :label="$t('Bid.Name')" prop="Name">
            <el-input
              v-model="view.Bider.Name"
              :placeholder="$t('Bid.Name')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('Bid.ContactPerson')" prop="Contact">
            <el-input
              v-model="view.Bider.Contact"
              :placeholder="$t('Bid.ContactPerson')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('Bid.Email')" prop="Email">
            <el-input
              v-model="view.Bider.Email"
              :placeholder="$t('Bid.Email')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('Bid.Phone')" prop="WhatsApp">
            <el-input
              v-model="view.Bider.WhatsApp"
              :placeholder="$t('Bid.Phone')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('Bid.Fax')" prop="Fax">
            <el-input
              v-model="view.Bider.Fax"
              :placeholder="$t('Bid.Fax')"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('Bid.Address')" prop="Address">
            <el-input
              v-model="view.Bider.Address"
              :placeholder="$t('Bid.Address')"
            ></el-input>
          </el-form-item>
          <div class="bottomBtn">
            <div class="left"></div>
            <div class="right">
                <a href="javascript:;" @click="SubmitForm('Bider')">{{$t('Bid.Signin')}}</a>
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
@Component({
  components: {}
})
export default class InsertBider extends Vue {
  view: any={
    Bider: {
      Name: '',
      Contact: '',
      Email: '',
      WhatsApp: '',
      Fax: '',
      Address: ''
    }
}
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
      Email: [
          { validator: this.checkEmail, trigger: 'blur', required: true }
       ],
      WhatsApp: [
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
  SubmitForm (Bider) {
    let that = this;
    (that.$refs.Bider as ElForm).validate(valid => {
      if (valid) {
        this.$Api.enquiry.SaveBider(this.view).then(result => {
           if (result.Succeeded) {
              this.$message({
                message: this.$t('Message.SubmitSuccess') as string,
                type: 'success',
                customClass: 'messageboxNoraml'
              });
              setTimeout(() => {
                this.view.Bider={}
              },1000)
           }
        });
      } else {
        console.log('error submit!!');
        return false;
      }
    });
  }
  created() {}
  mounted() {}
}
</script>
<style scoped lang="less">
.Container {
    width: 100%;
    display: inline-block;
    background-size: cover;
    .Warper {
        width: 500px;
        margin: 0 auto;
        margin-top: 50px;
        padding-bottom: 100px;
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
            color: @base_color;
            font-size: 26px;
            text-align: center;
            font-family: 'Domine-Bold','SourceHanSerifCN-Bold'!important;
        }
        .bottomBtn {
            width: 100%;
            display: flex;
            justify-content: space-between;
            .left {
                width: 120px;
            }
            .right {
                width: calc(100% - 120px);
                a{
                    background: @base_color;
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
