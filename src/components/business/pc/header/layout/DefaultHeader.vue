<template>
<div class="header-layout"  v-cloak>
  <div class="headerBg">
      <div class="NewTop">
        <div class="NewTop_w">
          <div class="function">
                <!-- <CodeSelect  /> -->
                <InsLogin class="memberLogin"></InsLogin>
                  <router-link to="/account/GetEnquiry">
                        <i class="ptxicon"></i>
                  </router-link>
                <InsLangSwitch></InsLangSwitch>

           </div>
        </div>
        <!-- <div class="right"> -->

          <!-- <div class="search-box">
              <div class="search-input">
                  <span class="searchBtn" @click="search"><img src="/images/mobile/m_13.jpg"></span>
                  <input type="text" v-model="key" @keyup.enter="search" />
              </div>
          </div> -->
          <!-- <div class="BecomeUs">
              <router-link class="Content" to="/Enquiry/InsertBider">{{$t('Bid.Becomeoursupplier')}}</router-link>
          </div> -->
        <!-- </div> -->
      </div>
      <div class="MeunTop">
        <a href="/" class="logo"><img src="/images/pc/PC_logo.png"></a>
          <Menu />
      </div>
  </div>
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component({
  components: {
    Menu: () =>
      import('@/components/business/pc/header/InsMenu.vue'),
    Shopcart: () =>
      import('@/components/business/pc/header/InsShoppingCart.vue'),
    InsLogin: () =>
      import('@/components/business/pc/header/InsLogin.vue'),
    InsLangSwitch: () =>
      import('@/components/business/pc/header/InsLangSwitch.vue'),
    CodeSelect: () =>
      import('@/components/business/pc/header/InsCodeSelect.vue')
  }
})
export default class DefaultHeader extends Vue {
  @Prop() private showInFixed!: boolean;

  private key: string = '';

  private typeList: any[] = [{
    value: 0,
    label: 'Product'
  }, {
    value: 1,
    label: 'CMS'
  }];

  private searchType: number = 0;
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  getMenu () {
    this.$Api.promotion
      .getMenu()
      .then(result => {
        this.$store.dispatch('setHeaderMenus', result.ReturnValue.HeaderMenus);
        this.$store.dispatch('setFooterMenus', result.ReturnValue.FooterMenus);
      })
      .catch(error => {
        console.log(error);
      });
  }

  search () {
    switch (this.searchType) {
      case 0:
        this.searchPro();
        break;
      case 1:
        this.searchCms();
        break;
    }
  }

  searchPro () {
    this.$store.dispatch('setSearchKey', this.key);
    if (this.key !== '') {
      this.$router.push({
        path: '/product/search',
        name: 'productSearch',
        params: {
          key: this.key
        }
      });
    } else {
      this.$router.push({
        path: '/product/search/-'
      });
    }
  }

  searchCms () {
    this.$router.push({
      path: '/cms/search',
      name: 'cmsSearch',
      params: {
        key: this.key
      }
    });
  }

  get currentlang () {
    return this.$i18n.locale;
  }
  private changLange (lang) {
    this.$Api.member
      .setUILanguage(lang)
      .then(result => {
        this.$i18n.locale = lang;
        localStorage.setItem('locale', lang);
        this.Reload();
      })
      .catch(error => {
        console.log(error);
      });
  }

  created () {
    this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
  }

  mounted () {
    this.getMenu();
  }
}
</script>

<style scoped lang="less">

// new css
.header-layout {
  width: 100%;
  display: block;
  .headerBg {
    background: #fff;
    box-shadow: 0 0 10px 0 #d4d5d1;
  }
 .NewTop {
  width: 100%;
  height: 40px;
  background-color: #999999;
  .NewTop_w {
    width: 1200px;
    margin: 0 auto;
    display: flex;
    justify-content: right;
    .function {
      display: flex;
      align-items: center;
      justify-content: flex-end;
      // padding-top: 10px;
      height: 40px;
      .memberLogin {
        position: relative;
      }
    }
  }
  .right {
    width: 60%;
    position: relative;
    .BecomeUs {
      position: absolute;
      top: 0;
      left: 0;
      .Content {
        background: #e46c0a;
        color: #fff;
        height: 40px;
        line-height: 40px;
        color: #fff;
        font-size: 20px;
        padding-left: 20px;
        padding-right: 20px;
        border-bottom-left-radius: 30px;
        border-bottom-right-radius: 30px;
        border: 3px solid #ffbf00;
        border-top: 0px;
        display: inline-block;
        transition: all .5s;
        &:hover {
          border: 3px solid #e46c0a;
          border-top: 0px;
        }
      }
    }

    .ptxicon {
      width: 30px;
      height: 30px;
      background: url('/images/mobile/m_02.jpg') no-repeat center center;
      background-size: cover;
      display: inline-block;
    }
.search-box {
    width: 100%;
    overflow: hidden;
    margin-bottom: 1rem;
    padding-top: .5rem;
    padding-bottom: .5rem;
    justify-content: flex-end;
  .search-input {
    width: 400px;
    height: 40px;
    background: #fff;
    border-radius: 5px;
    overflow: hidden;
    position: relative;
    display: flex;
    align-items: center;
    float: right;
    border: 1px solid #eee;
    > input {
        width: 100%;
        height: 100%;
        border: 0;
        padding-right: 4rem;
        box-sizing: border-box;
        font-size: 16px;
        outline: none;
        padding-left: 1rem;
    }

    .searchBtn{
        width: 30px;
        height: 30px;
        display: inline-block;
        cursor: pointer;
        position: absolute;
        right: 0;
        top: 0;
        display: flex;
        align-items: center;
        justify-content: center;
        border-left: 1px solid #eee;
        margin-right: 1rem;
        margin-top: 5px;
        padding-left: 5px;
        img{
            width: 100%;
            margin: 0 auto;
            display: block;
        }
    }
  }
}
  }
 }
  .MeunTop{
    width: 1200px;
    margin: 0 auto;
    height: 100px;
    .logo{
      float: left;
      width: 262px;
      margin-top: 13px;
    }
  }
 /deep/ .header_menu {
   float: right;
    width: 938px;
   > ul {
    justify-content: right;
    margin-top: 52px;
     > li {
      float: left;
      display: flex;
      align-items: center;
      position: relative;
      margin-right: 0;
      > a {
        width: 100%;
        font-size: 16px;
        color: #333333;
        display: block;
        text-align: center;
        font-weight: 500;
        padding: 10px 25px;
        font-family: 'SourceHanSerifCN-Regular';
        font-weight: bold;
        position: relative;
      }
      .router-link-active{
        color: @base_color;
          &::before{
            content: '';
            width: 100%;
            height: 4px;
            background-color: #3c408e;
            position: absolute;
            left: 0;
            bottom: 0;
          }
      }
      &:hover{
        > a  {
          color: @base_color;
          &::before{
            content: '';
            width: 100%;
            height: 4px;
            background-color: #3c408e;
            position: absolute;
            left: 0;
            bottom: 0;
          }
          .showIcon {
            display: inline-block;
            margin-right: 5px;
            background: transparent url('/images/mobile/m_44.jpg') no-repeat 10% center;
            background-size: 20px;
          }
        }
      }

      ul {
        box-shadow: 0 0 5px #ccc;
        width: 150%;
        left: 50%;
        transform: translate(-50%);
        li {
          border: 0;
          border-bottom: 1px solid #eee;
          > a {
            font-size: 16px;
            color: #999999;
            display: block;
            text-align: center;
            font-weight: 500;
            padding: 10px 5px;
          }

          &:hover{
             > a {
              background: #fff;
              color: @base_color;
            }
          }
        }
      }
     }
   }
 }
}
</style>
