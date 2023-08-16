<template>
  <div class="Container">
    <transition name="slide">
      <div key="1" v-if="!waiting">
        <img :src="Inquiry.Cover" width="100%" />
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
    <div class="Warper">
      <div class="perData" v-for="(v, index) in ProductList" :key="index">
        <div class="Inner">
          <div class="right">
            <p class="Title" :class="{'Etitle':currentlang==='E'}">{{ v.Name }}</p>
            <div class="perProduct">
              <div
                class="perCheck"
                v-for="(t, indexB) in v.Products"
                :key="indexB"
                @click="SelectFun(index,indexB)"
              >
                <span class="checkbox"
                  ><img :src="t.IsComb?'/images/pc/tc02.jpg':'/images/pc/tc01.jpg'"
                /></span>
                <span class="text" :class="t.IsComb?'addText':''">{{ t.Title }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="Qbtn">
        <a class="btn" href="javascript:;" @click="AddProdsToMyEnquiry()">{{$t('Bid.QueryQuotation')}}</a>
      </div>
    </div>
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component({
  components: {}
})
export default class Bid extends Vue {
  Inquiry: string = '';
  CatList: any = [];
  ProductList: any = [];
  private waiting: boolean = true;
  SelectList: any = [];
  getContent() {
    this.$Api.cms
      .getContentByDevice({ Key: 'Inquiry', IsMobile: true })
      .then((result) => {
        this.Inquiry = result.CMS;
        this.waiting = false;
        if (result.CMS.Title) document.title = result.CMS.Title;
      });
  }
  get currentlang () {
    return this.$Storage.get('locale');
  }
  AddProdsToMyEnquiry() {
    if (!this.$Storage.get('isLogin')) {
       Vue.prototype.$Confirm(this.$t('product.logouted'), this.$t('product.loginow'), () => { this.$Login(this.AddProdsToMyEnquiry); });
    } else {
      if (this.SelectList.length > 0) {
        var obj = {
          items: {}
        };
        obj.items = this.SelectList;
        this.$Api.enquiry.AddProdsToMyEnquiry(obj).then(result => {
          console.log(result, 'resultresultresult');
          if (result.data.Succeeded) {
            this.$router.push('/account/GetEnquiry');
          } else {
              this.$message({
                message: result.data.Message as string,
                type: 'error',
                customClass: 'messageboxNoraml'
              });
          }
        });
      } else {
          this.$message({
            message: this.$t('Bid.Pleaseticktheservice') as string,
            type: 'error',
            customClass: 'messageboxNoraml'
          });
      }
    }
  }
  getCatList() {
    this.$Api.product.getAttrList().then((result) => {
      this.ProductList = result;
    });
  }
  SelectFun(indexA, indexB) {
    var that = this;
    var a = indexA;
    var b = indexB;
    var arr = [];
    var obj = {};
    var ProductList = that.ProductList;
    that.SelectList = [];
    ProductList[a].Products[b].IsComb = !ProductList[a].Products[b].IsComb;
    this.$forceUpdate();
     this.ProductList.forEach((item) => {
      item.Products.forEach((value) => {
        if (value.IsComb === true) {
         obj = {
            Sku: value.Sku,
            Attr1: 1,
            Attr2: 1,
            Attr3: 1,
            Qty: 1
          };
          that.SelectList.push(obj);
        }
      });
    });
  }
  created() {
    this.getContent();
    this.getCatList();
  }
}
</script>
<style scoped lang="less">
.Container {
  width: 100%;
  display: inline-block;
  background-size: cover;
  padding-bottom: 80px;
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
  .Warper {
    width: 90%;
    margin: 0 auto;
    padding-top: 20px;
    .perData {
      width: 100%;
      display: inline-block;
      margin-bottom: 50px;
      border-radius: 5px;
      border-bottom: 1px solid #eee;
      padding-bottom: 20px;
      .Inner {
        padding: 0px;
        display: flex;
        align-items: center;
        width: 100%;
        .right {
          width: 100%;
          .Title {
            font-size: 2rem;
            color: #333333;
            padding-right: 10px;
            display: flex;
            align-items: center;
            margin-bottom: 1rem;
            padding-bottom: 1rem;
            font-family: 'SourceHanSerifCN-Heavy';
            &::before {
              content: '';
              height: 10px;
              width: 10px;
              border-radius: 10px;
              background: @base_color;
              display: inline-block;
              margin-right: 10px;
            }
          }
          .Etitle {
            font-size: 1.6rem!important;
          }
          .perProduct {
            width: 100%;
            display: inline-block;
            .perCheck {
              width: 100%;
              margin-bottom: 1rem;
              cursor: pointer;
              display: inline-block;
              .checkbox {
                width: 15px;
                height: 15px;
                margin-right: 10px;
                float: left;
                padding-top: 2px;
                img {
                  width: 100%;
                }
              }
              .text {
                font-size: 1.4rem;
                float: left;
                width: calc(100% - 30px);
              }
              .addText {
                color: @base_color!important;
              }
              &:nth-child(3n) {
                margin-right: 0px !important;
              }
            }
          }
        }
      }
    }
  }
  .Qbtn {
    width: 90%;
    margin: 0 auto;
    text-align: center;
    a{
      display: inline-block;
      padding-left: 50px;
      padding-right: 50px;
      background: @base_color;
      height: 45px;
      color: #fff;
      line-height: 45px;
      font-size: 20px;
      border-radius: 5px;
      transition: all .5s;
    }
  }
}
</style>
