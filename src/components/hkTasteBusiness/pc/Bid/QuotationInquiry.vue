<template>
  <div class="Container">
    <transition name="slide">
      <div key="1" v-if="!waiting">
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
    <div class="Warper">
      <div class="perData" v-for="(v, index) in ProductList" :key="index">
        <div class="Inner">
          <div class="right">
            <p class="Title">{{ v.Name }}</p>
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
      .getContentByDevice({ Key: 'Inquiry', IsMobile: false })
      .then((result) => {
        this.Inquiry = result.CMS;
        this.waiting = false;
        if (result.CMS.Title) document.title = result.CMS.Title;
      });
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
  .BannerTop {
    width: 1200px;
    margin: 0 auto;
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
  .Warper {
    width: 1200px;
    margin: 0 auto;
    padding-top:20px;
    padding-bottom: 50px;
    .perData {
      width: calc(100% - 2px);
      display: inline-block;
      margin-bottom: 50px;
      .Inner {
        padding: 0px;
        display: flex;
        .right {
          width: 100%;
          .Title {
            font-size: 24px;
            color: #333333;
            padding-right: 50px;
            margin-bottom: 20px;
            font-family: 'Domine-Bold','SourceHanSerifCN-Bold'!important;
            display: flex;
            align-items: center;
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
          .perProduct {
            width: 100%;
            display: inline-block;
            .perCheck {
              float: left;
              margin-right: 4.5%;
              margin-bottom: 2%;
              cursor: pointer;
              display: flex;
              align-items: center;
              .checkbox {
                width: 20px;
                height: 20px;
                display: inline-block;
                margin-right: 10px;
                img {
                  width: 100%;
                }
              }
              .text {
                font-size: 18px;
                color: #999;
              }
              .addText {
                color: @base_color!important;
              }
            }
          }
        }
      }
    }
  }
  .Qbtn {
    width: 1200px;
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
      &:hover {
        background: #333;
      }
    }
  }
}
</style>
