<template>
<div class="warper NomralBg NormalTop">
    <transition name="slide">
      <div key="1" v-if="!waiting" class="warperTop">
        <div class="BannerTop">
          <img :src="Inquiry.Cover" width="100%" />
        </div>
      </div>
    </transition>
    <transition name="slide">
      <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
    </transition>
      <div class="NormalTitle">
      <i class="left"></i><span class="center">{{ Inquiry.Title }}</span
      ><i class="right"></i>
    </div>
    <div class="Location"><span class="HomeText">{{$t('Message.Home')}} > </span><span class="ColorText">{{ Inquiry.Title }}</span></div>
    <el-form
        :model="ruleForm"
        :rules="rules"
        :label-position="'right'"
        label-width="120px"
        ref="ruleForm"
        class="demo-ruleForm"
    >
    <div class="Inner">
        <div class="left">
                <p class="HeadTitle NHeadTitle">
                    <span class="Request" v-if="ruleForm.CaseCode">{{$t('Enquiry.EnquiryTitle')}}: {{ruleForm.CaseCode}}</span>
                    <span class="Request" v-else>{{$t('Enquiry.EnquiryTitle')}}: Request</span>
                </p>
                <div class="TableA" style="padding-top:15px;margin-bottom:50px">
                    <ul>
                        <li>
                            <span class="w15">{{$t('Enquiry.Date')}}:</span>
                            <span class="w83">{{ruleForm.CreateDate}}</span>
                        </li>
                        <el-form-item>
                            <li>
                                <span class="w15">{{$t('Enquiry.YourRef')}}:</span>
                                <span class="w83"> <el-input v-model="ruleForm.CustomerView.ClientCode" disabled></el-input></span>
                            </li>
                        </el-form-item>
                    </ul>
                </div>
                <div class="NewTable">
                    <div class="TableA Recipient">
                                <div class="BgTitle">
                                    <p class="InnerTitle">{{$t('Enquiry.From')}}</p>
                                </div>
                                <ul>
                                    <li>
                                        <span class="w50">{{$t('Enquiry.FirstName')}}:{{ruleForm.CustomerView.FirstName}}</span>
                                        <span class="w50">{{$t('Enquiry.LastName')}}:{{ruleForm.CustomerView.LastName}}</span>
                                    </li>
                                    <li>
                                        <span class="w50">{{$t('Enquiry.Title')}}:{{ruleForm.CustomerView.JobTitle}}</span>
                                        <span class="w50">{{$t('Enquiry.Company')}}:{{ruleForm.CustomerView.CompanyName}}</span>
                                    </li>
                                    <li>
                                        <span class="w50">{{$t('Enquiry.Address')}}:{{ruleForm.CustomerView.Address}}</span>
                                        <span class="w50">{{$t('Enquiry.City')}}:{{ruleForm.CustomerView.CityName}}</span>
                                    </li>
                                    <li>
                                        <span class="w50">{{$t('Enquiry.State')}}:{{ruleForm.CustomerView.ProvinceName}}</span>
                                        <span class="w50">{{$t('Enquiry.PostalCode')}}:{{ruleForm.CustomerView.PostalCode}}</span>
                                    </li>
                                <li>
                                        <span class="w50">{{$t('Enquiry.Country')}}:{{ruleForm.CustomerView.CountryName}}</span>
                                        <span class="w50">{{$t('Enquiry.Phone')}}:{{ruleForm.CustomerView.Phone}}</span>
                                    </li>
                                    <li>
                                        <span class="w50">{{$t('Enquiry.Mobile')}}:{{ruleForm.CustomerView.Mobile}}</span>
                                        <span class="w50">{{$t('Enquiry.Fax')}}:{{ruleForm.CustomerView.Fax}}</span>
                                    </li>
                                        <li>
                                        <span class="w50">{{$t('Enquiry.Email')}}:{{ruleForm.CustomerView.Email}}</span>
                                        <span class="w50"></span>
                                    </li>
                                </ul>
                            </div>
                            <div class="TableA">
                                <ul>
                                    <el-form-item>
                                        <li>
                                            <p>{{$t('Enquiry.ShippingInformation')}}:</p>
                                            <p> <el-input v-model="ruleForm.DeliveryInfo" :disabled="isPreview"></el-input></p>
                                        </li>
                                </el-form-item>
                                </ul>
                            </div>
                </div>
<!--                 <div class="NewTable">
                    <div class="TableA Recipient">
                        <div class="BgTitle">
                            <p class="InnerTitle">{{$t('Enquiry.NegotiationBox')}}</p>
                        </div>
                        <ul>
                            <el-form-item class="xsmessage">
                                <li>
                                    <p>{{$t('Enquiry.leavemessage')}}</p>
                                    <p><el-input v-model="MessageContent" width="90%" :disabled="isPreview"></el-input></p>
                                </li>
                        </el-form-item>
                        </ul>
                    </div>
                </div> -->
        </div>
        <div class="NewTable">
            <div class="NewHeadTitle">
                <div class="WarpInner">
                    <span class="Text">{{$t('Enquiry.DraftEnquiry')}}</span>
                    <span class="select">
                        <input type="checkbox"
                        v-model="isAll"
                        @click="checkAll($event)"
                        class="checkBox"
                        >
                       <label>{{$t('Bid.SelectAll')}}</label>
                        </span>
                </div>
            </div>
            <div class="InnerBox">
                <div class="productBox">
                    <div class="PerData" v-for="(v,index) in ruleForm.CatalogDetailList" :key="index">
                        <p class="Title"><span>{{v.Name}}</span></p>
                        <div class="CheckData" v-for="(t,indexB) in v.DetailList" :key="indexB">
                            <div class="left">
                            <el-checkbox-group v-model="SelectId" @change="selectAttr(t.Id)">
                                <el-checkbox :label="t.Id" ><br></el-checkbox>
                            </el-checkbox-group>
                            </div>
                            <div class="right">
                                <p class="pTitle">{{t.ProductName}}</p>
                                <p class="remark">
                                    <span class="inputText">
                                        <el-input v-model="t.Remark" :disabled="isPreview" :placeholder="$t('Enquiry.OtherRequirement')"></el-input>
                                    </span>
                                    </p>
                            </div>
                        </div>
                    </div>
                    <div class="BottomBtn">
                        <span @click="DelOrderDetails()">{{$t('Enquiry.Delete')}}</span>
                        <span @click="AddProduct()">{{$t('Enquiry.AddProduct')}}</span>
                    </div>
                </div>
                <div class="DeliverBox">
                    <el-form-item>
                        <div class="PerList">
                            <div class="left">{{$t('Enquiry.DeliverMethod')}}:</div>
                            <div class="right"><el-input v-model="ruleForm.DeliveryType" :disabled="isPreview"></el-input></div>
                        </div>
                    </el-form-item>
                     <el-form-item>
                        <div class="PerList">
                            <div class="left">{{$t('Enquiry.ShipmentTerms')}}:</div>
                            <div class="right"><el-input v-model="ruleForm.ShipCondition" :disabled="isPreview"></el-input></div>
                        </div>
                    </el-form-item>
                     <el-form-item>
                        <div class="PerList">
                            <div class="left">{{$t('Enquiry.PaymentTerms')}}:</div>
                            <div class="right">
                                <el-select v-model="ruleForm.PayType" :disabled="isPreview">
                                 <el-option value="">{{$t('Bid.PleaseSelectaPaymentMethod')}}</el-option>
                                <el-option v-for="(pay, index) in payments" :key="index" :value="pay.Id" :label="pay.Desc"></el-option>
                            </el-select>
                            </div>
                        </div>
                    </el-form-item>
                    <el-form-item>
                        <div class="PerList">
                            <div class="left">{{$t('Enquiry.Remarks')}}:</div>
                            <div class="right">
                                <el-input v-model="ruleForm.Remark" :disabled="isPreview"></el-input>
                            </div>
                        </div>
                    </el-form-item>
                </div>
                <div class="NewSave" v-if="!isPreview">
                    <a @click="SaveDaft()"><span>{{$t('Enquiry.SaveDraft')}}</span></a>
                    <a @click="Preview()"><span>{{$t('Enquiry.PreviewBeforeSending')}}</span></a>
                </div>
                <div class="NewSave" v-else>
                    <a @click="SaveComfirm()"><span>{{$t('Enquiry.ConfirmandSend')}}</span></a>
                    <a @click="GoBack()" class="colorStyle"><span>{{$t('Enquiry.Back')}}</span></a>
                </div>
            </div>
        </div>
    </div>
     </el-form>
</div>
</template>
<script lang='ts'>
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import Express from '@/model/express';
import PaymentM from '@/model/payment';
@Component
export default class InsAddEnquiry extends Vue {
  private payments:PaymentM[] = [new PaymentM()];
  private payment: PaymentM = new PaymentM();
  Inquiry: string = '';
  private waiting: boolean = true;
  isPreview:boolean =false;
  CreateDate:string='';
  OrderId:string='';
  CaseId:string='';
  MessageContent:string='';
  SelectId:any[]=[];
  isAll: boolean = false; // 是否全选
  ruleForm: any = {
    Id: '',
    OrderType: 0,
    CustomerView: {},
    CaseView: {},
    DetailList: [{
        Qty: 0,
        Remark: ''
    }],
    DeliveryInfo: '',
    DeliverySchedule: '',
    DeliveryType: '',
    ShipCondition: '',
    Remark: '',
    IsActive: false,
    SiteLetterList: '',
    PayType: ''
  };
 getInquiry() {
    this.$Api.cms
      .getContentByDevice({ Key: 'Inquiry', IsMobile: true })
      .then((result) => {
        this.Inquiry = result.CMS;
        console.log(this.Inquiry, 'this.Inquiry');
        this.waiting = false;
      });
  }
  Preview () {
      this.isPreview = true;
  }
  GoBack () {
      this.isPreview = false;
  }
  //  全选
 checkAll (e) {
      let a = e.target.checked;
      this.SelectId = [];
      if (e.target.checked) {
         this.ruleForm.CatalogDetailList.forEach(element => {
            element.DetailList.forEach((v) => {
                this.SelectId.push(v.Id);
            });
          });
      }
    }
  selectAttr (item) {
        var NewArray:any = [];
        this.ruleForm.CatalogDetailList.forEach((item) => {
            item.DetailList.forEach((v) => {
                NewArray.push(v);
            });
        });
      setTimeout(() => {
          if (this.SelectId.length !== NewArray.length) {
            this.isAll = false;
          } else {
            this.isAll = true;
          }
      }, 1);
    }
  DelOrderDetails () {
      if (this.SelectId.length > 0) {
            var params = {
                OrderId: this.OrderId,
                Ids: this.SelectId
            };
            this.$Api.enquiry.DelOrderDetails(params).then(result => {
                if (result.Succeeded) {
                this.$message({
                    message: this.$t('Enquiry.SuccessfullyDeleted') as string,
                    type: 'success',
                    customClass: 'messagelayer'
                });
                this.GetMyEditingEnquiry();
                }
            });
      } else {
            this.$message({
                message: this.$t('Enquiry.Pleaseselect') as string,
                type: 'error',
                customClass: 'messagelayer'
            });
      }
  }
  AddProduct () {
      this.$router.push('/Enquiry/QuotationInquiry');
  }
  SaveDaft () {
      this.$Api.enquiry.SaveMyEnquiry(this.ruleForm).then(result => {
            if (result.Succeeded) {
            this.$message({
                message: this.$t('Enquiry.Savedsuccessfully') as string,
                type: 'success',
                customClass: 'messagelayer'
            });
          }
      });
  }
  SaveComfirm () {
        console.log(this.ruleForm, 'this.ruleFormthis.ruleForm');
       this.ruleForm.IsActive = true;
       this.$Api.enquiry.SaveMyEnquiry(this.ruleForm).then(result => {
          if (result.Succeeded) {
            this.SendMessage();
            setTimeout(() => {
                this.$router.push('/account/EnquirySuccess');
            }, 500);
          }
      });
  }
  SendMessage () {
        if (this.MessageContent !== '') {
            var params = {
            OrderId: this.CaseId,
            Content: this.MessageContent,
            IsBuyer: true
            };
            this.$Api.enquiry.CreateSiteLetter(params).then(result => {
            if (result.Succeeded) {
                console.log('dddddddddd0000000000000');
            }
            });
        }
  }
  clear () {
    this.MessageContent = '';
  }
  get rules () {
    return {
      FirstName: [
        {
          required: true,
          message: this.$t('MemberInfo.EnterUserName'),
          trigger: 'blur'
        }
      ]
    };
  }
  GetMyEditingEnquiry () {
      this.$Api.enquiry.GetMyEditingEnquiryV2().then(result => {
          this.ruleForm = result;
          this.OrderId = result.Id;
          this.CaseId = result.CaseId;
          console.log(result, 'ddddddddd');
      });
  }
  getpayments () {
     this.$Api.checkout.getPaymentMethod().then(result => {
        this.payments = result.Payment;
        console.log(result, 'resultresult');
      });
  }
  created() {
   this.getInquiry();
   this.GetMyEditingEnquiry();
   this.getpayments();
  }
}
</script>
<style scoped lang='less'>
.BannerTop {
    width: 100%;
    display: flex;
}
.Location {
    width: 90%;
    margin: 0 auto;
    margin-top: 2rem;
    .HomeText {
        font-size: 1.4rem;
        color:#999999;
    }
    .ColorText {
        font-size: 1.4rem;
        color: @base_color;
    }
}
/deep/ .el-form {
    width: 100%;
    .el-form-item {
        width: 100%;
        margin-bottom: 0px;
        .el-form-item__content {
            margin-left: 0px!important;
        }
    }
    .el-select {
        width: 100%;
    }
    .el-input.is-disabled .el-input__inner {
        background-color: #fff!important;
        border-color:#fff!important;
        color: #333!important;
        font-size: 1.2rem;
    }
    .el-checkbox__input.is-checked .el-checkbox__inner, .el-checkbox__input.is-indeterminate .el-checkbox__inner {
        background-color: @base_color;
        border-color:  @base_color;
    }
}
.xsmessage {
    /deep/ .el-input {
        width: 100%;
    }
    /deep/ .el-button {
        width: 10%;
    }
}
/deep/ .el-input__inner {
    border: 1px solid #eee;
    border-radius: 0px;
}
.clearBtn {
    background: @base_color;
    color: #fff;
}
.NewTable {
    box-shadow: 0px 0px 5px #ddd;
    border-radius: 5px;
    overflow: hidden;
    margin-bottom: 50px;
    width: calc(100% - 2px);
    .TableA {
        margin-top: 0px!important;
        .InnerTitle {
           color: @base_color;
            font-family: 'SourceHanSerifCN-Bold';
            &::before {
                width: 20px!important;
                height: 20px!important;
                background: url('/images/mobile/m_44.jpg') no-repeat center center;
                background-size: cover;
            }
        }
        .BgTitle {
            background: #fff;
        }
    }
    .NewHeadTitle {
        padding-top: 10px;
        padding-bottom: 10px;
        color: @base_color;
        .WarpInner {
            color: #fff;
            font-size: 20px;
            width: 95%;
            margin: 0 auto;
            display: flex;
            align-items: center;
            justify-content: space-between;
            .Text {
                color: @base_color;
                font-family: 'SourceHanSerifCN-Bold';
                font-size: 1.4rem;
                &::before {
                    content:'';
                    width: 20px!important;
                    height: 20px!important;
                    background: url('/images/mobile/m_44.jpg') no-repeat center center;
                    background-size: cover;
                    display: inline-block;
                    margin-right: 5px;
                }
            }
            .select {
                display: flex;
                align-items: center;
                input[type="checkbox"] {
                    width: 15px;
                    height: 15px;
                    background-image: url('/images/pc/tc01.jpg');
                    appearance:none;
                    background-repeat: no-repeat;
                    background-position: center;
                    background-size: auto;
                    border: 1px solid #eee;
                    background-size: cover;
                }
                label {
                    font-size: 1.2rem;
                    color: #666666;
                    width: 100%;
                    text-align: center;
                    display: flex;
                    align-items: center;
                    justify-content: center;
                }
                input[type="checkbox"]:checked {
                    background-image: url('/images/pc/tc02.jpg'); /*复选框的背景图*/
                    background-repeat: no-repeat;
                    background-position: center;
                    background-size: cover;

                    &+label {
                        color:#e46c0a;
                        background: url('/images/mobile/ptx_27.png') no-repeat center center;
                        background-size:cover;
                    }
                }
            }
        }
    }
    .InnerBox {
        width: 90%;
        margin: 0 auto;
        padding-top: 30px;
        padding-bottom: 30px;
        .productBox {
            padding-bottom: 50px;
            border-bottom: 1px solid #eee;
            .PerData {
                width: 100%;
                display: inline-block;
                margin-bottom: 20px;
                &:last-child{
                    margin-bottom: 0px!important;
                }
                .Title {
                    position: relative;
                    width: 100%;
                    display: flex;
                    align-items: center;
                    span{
                        color: #333333;
                        font-size: 1.6rem;
                        float: left;
                        margin-left: 10px;
                        width: 100%;
                        font-family: 'SourceHanSerifCN-Heavy';
                    }
                    &::before {
                        content: '';
                        height: 6px;
                        line-height: 6px;
                        background: @base_color;
                        border-radius: 6px;
                        width: 6px;
                        display: block;
                        float: left;
                    }
                }
                .CheckData {
                    width: 100%;
                    display: flex;
                    justify-content: space-between;
                    margin-top: 20px;
                    align-items: baseline;
                    .left {
                        width: 20px;
                        height: 20px;
                    }
                    .right {
                        width: 97%;
                        .pTitle {
                            font-size: 1.2rem;
                        }
                        .remark {
                            width: 100%;
                            display: flex;
                            padding-top: 20px;
                            .OtherRequirement {
                                width: 32%;
                                font-size: 1.2rem;
                            }
                            .inputText {
                                width: 100%;
                            }
                        }
                    }
                }
            }
        }
    }
    .BottomBtn {
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: flex-end;
        span{
            width: 100px;
            height: 40px;
            background: @base_color;
            color: #fff;
            margin-left: 20px;
            border-radius: 3px;
            text-align: center;
            transition: all .5s;
            font-size: 1.2rem;
            display: flex;
            align-items: center;
            justify-content: center;
            &:nth-child(1) {
                background: #b3b3b3!important;
                &:hover {
                    background: #333;
                }
            }
        }
    }
    .DeliverBox {
        padding-top: 20px;
        .PerList {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            margin-bottom: 20px;
            align-items: center;
            .left {
                width: 100%;
                font-size: 1.2rem;
                line-height: 24px;
                display: flex;
                flex-wrap: wrap;
                margin-bottom: .5rem;
            }
            .right {
                width: 100%;
                display: flex;
                flex-wrap: wrap;
            }
        }
    }
    .NewSave {
        width: 100%;
        display: flex;
        justify-content: center;
        a{
            display: flex;
            color: @base_color;
            border: 1px solid @base_color;
            border-radius: 3px;
            text-align: center;
            justify-content: center;
            transition: all .5s;
            cursor: pointer;
            font-size: 1.4rem;
            align-items: center;
            justify-content: center;
            width: 42%;
            align-items: center;
            height: 40px;
            span{
                font-size: 1.2rem;
            }
            &:nth-child(1) {
                width: 55%!important;
                background: @base_color;
                color: #fff!important;
                margin-right: 3%;
            }
            &:hover {
                background: @base_color;
            }
        }
    }
}
.NHeadTitle {
    padding-bottom: 10px;
    &::before {
        background:@base_color!important;
    }
    .Request {
        color: #333333;
        font-family: 'SourceHanSerifCN-Bold';
    }
}
.InnerUl {
    width: 95%;
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
    .checkBox {
        box-sizing: border-box;
        width: 14px;
        height: 14px;
        border: 1px solid #DCDFE6;
        outline: 0;
        background: #fff;
        border-radius: 2px;
    }
}
.warper .BottomTable {
    padding-top: 22px;
    padding-bottom: 22px;
}
.warper .BottomTable ul li {
    border-bottom: 0px!important;
    padding-top: 5px;
    padding-bottom: 5px;
}
.colorStyle {
    background: #ddd!important;
    color: #333!important;
}
.Inner {
    margin-top: 30px;
    margin-bottom: 30px;
}
.DelectBtn {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    background: #7f8dba;
    margin-top: 10px;
    padding-top: 5px;
    padding-bottom: 5px;
    span{
      color: #333;
       margin-left: 2.5%;
       border: 1px solid #eee;
       display: inline-block;
       text-align: center;
       cursor: pointer;
       background: #fff;
       padding: 8px 20px;
       transition: all .3s;
       &:hover {
           background: #333;
           color: #fff;
           border: 1px solid #333;
       }
    }
}
</style>
