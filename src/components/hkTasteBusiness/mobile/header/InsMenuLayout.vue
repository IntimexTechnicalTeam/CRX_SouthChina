<template>
    <div>
        <div class="header_logo">
          <div class="Inner">
            <div class="left">
                <LangSwitch type="LangSelect" class="headerLang" />
                <!-- <InsCodeSelect/> -->
            </div>
            <div class="right">
              <img class="slide-menu" src="/images/mobile/m_64.png" @click="showSlideMenu" />
                 <!-- <router-link to="/account/GetEnquiry">
                    <i class="handle-icon ptxicon"></i>
                </router-link>
                <ins-login /> -->
            </div>
          </div>
        </div>
        <!-- <div class="search-box">
            <div class="search-input">
                <span class="searchBtn" @click="search"><img src="/images/mobile/m_13.jpg"></span>
                <input type="text" v-model="key" @keyup.enter="search" />
            </div>
        </div> -->
        <div id="menu">
            <Menu :backColor="'@base_color'" :textColor="'#fff'" :uniqueOpened="true" />
        </div>
        <div class="login">
          <ins-login />
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component({
  components: {
    Menu: () => import('@/components/business/mobile/header/InsElMenu.vue'),
    LangSwitch: () => import('@/components/business/mobile/header/InsLangSwitch.vue'),
    InsLogin: () => import('@/components/business/mobile/header/InsLogin.vue'),
    InsCodeSelect: () => import('@/components/business/mobile/header/InsCodeSelect.vue')
  }
})
export default class InsMenuLayout extends Vue {
  showMemNav: boolean = false;
  private key: string = '';
  private searchType: number = 0;

  get menuShow () {
    return this.$store.state.isShowMenu;
  }

  showSlideMenu () {
    let isShow = !JSON.parse(JSON.stringify(this.menuShow));
    this.$store.dispatch('isShowMenu', isShow);
  }

  handleOpen (key, keyPath) {
    console.log(key, keyPath);
  }
  handleClose (key, keyPath) {
    console.log(key, keyPath);
  }

  closeSlideMenu () {
    this.$store.dispatch('isShowMenu', false);
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
    this.$store.dispatch('isShowMenu', false);
    if (this.key !== '') {
      this.$router.push({
        path: '/product/search',
        name: 'productSearch',
        params: {
          key: this.key
        }
      });
    } else {
        this.$store.dispatch('isShowMenu', false);
      this.$router.push({
        path: '/product/search/-'
      });
    }
  }

  searchCms () {
    this.$store.dispatch('isShowMenu', false);
    this.$router.push({
      path: '/cms/search',
      name: 'cmsSearch',
      params: {
        key: this.key
      }
    });
  }

  get user () {
    return this.$store.state.user;
  }

  get isLogin () {
    return this.$store.state.isLogin;
  }
}
</script>

<style lang="less">
.sidebar-container {
    background-color: #fff !important;
    z-index: 10000!important;
}
#menu {
    .el-submenu__icon-arrow {
        display: none;
    }

    .el-submenu__title {
        padding-top: 0.375rem;
        padding-bottom: 0.375rem;
        height: auto!important;
        line-height: unset;
        background-color:#fff!important;
        .name{
            font-size: 1.6rem!important;
            color:#333333;
            font-family: 'Domine-Bold','SourceHanSerifCN-Bold'!important;
        }
        i{
          color: #333333;
        }
    }

    .el-menu {
        width: 90%;
        margin: 0 auto;
        background-color: transparent;
        border: 0;
        margin-bottom: 1.5rem;
        margin-top: 1.5rem;
        border: 1px solid #999999;
        border-radius: 8px;
        overflow: hidden;
        box-sizing: border-box;
        .el-submenu__icon-arrow {
            display: block;
            font-size: 1.6rem;
        }

        > li {
            height: auto;
            line-height: unset;
            text-align: left;
            // margin-bottom: 1rem;
            border-bottom: 1px solid #999999;
            box-sizing: border-box;
            position: relative;
            &:last-child{
              border-bottom: none;
            }

             >a {
                 color:#333;
                 background-size: 100% 100%;
                 display:block;
                 width: 100%;
                 padding-top: 1rem;
                 padding-bottom: 1rem;
                 margin: 0 auto;
                 font-weight: 500;
                 font-size: 1.4rem;
                  font-family: 'SourceHanSerifCN-Regular';
                  padding-left: 4rem;
                  box-sizing: border-box;
                  position: relative;
                  font-weight: bold;

                 b{
                     color:#333333;
                     display: block;
                     width: 100%;
                     font-weight: 500;
                     font-size: 1.4rem;
                    font-family: 'SourceHanSerifCN-Regular';
                    font-weight: bold;
                    //  &:nth-child(1){
                    //     color:#333333;
                    //     font-weight: 500;

                    //     white-space: normal;
                    //     font-family: 'SourceHanSerifCN-Bold';
                    //  }
                    //  &:nth-child(2){
                    //      color:#333333;
                    //      font-size: 1.2rem;
                    //      white-space: normal;
                    //      font-family: 'SourceHanSerifCN-Bold';
                    //  }
                 }

            }

            a {
                text-decoration: none;
            }
            &.el-menu-item.is-active{
              background-color: #3c408e !important;
              a{
                color: #fff;
                &::before{
                  content: '';
                  width: 2rem;
                  height: 2rem;

                  position: absolute;
                  left: 1rem;
                  top: 50%;
                  transform: translateY(-50%);
                }
                b{
                  color: #fff;
                }
              }
              &:nth-child(1) a::before{
                background: url('/images/mobile/iconhover_07.png') no-repeat left center;
                background-size: contain;
              }
              &:nth-child(2) a::before{
                background: url('/images/mobile/iconhover_10.png') no-repeat left center;
                background-size: contain;
              }
              &:nth-child(3) a::before{
                background: url('/images/mobile/iconhover_12.png') no-repeat left center;
                background-size: contain;
              }
              &:nth-child(4) a::before{
                background: url('/images/mobile/iconhover_14.png') no-repeat left center;
                background-size: contain;
              }
              &:nth-child(5) a::before{
                background: url('/images/mobile/iconhover_16.png') no-repeat left center;
                background-size: contain;
              }
            }
        }
        li a::before{
          content: '';
          width: 2rem;
          height: 2rem;

          position: absolute;
          left: 1rem;
          top: 50%;
          transform: translateY(-50%);
        }
        li:nth-child(1) a::before{
          background: url('/images/mobile/icon_07.png') no-repeat left center;
          background-size: contain;
        }
        li:nth-child(2) a::before{
          background: url('/images/mobile/icon_10.png') no-repeat left center;
          background-size: contain;
        }
        li:nth-child(3) a::before{
          background: url('/images/mobile/icon_12.png') no-repeat left center;
          background-size: contain;
        }
        li:nth-child(4) a::before{
          background: url('/images/mobile/icon_14.png') no-repeat left center;
          background-size: contain;
        }
        li:nth-child(5) a::before{
          background: url('/images/mobile/icon_16.png') no-repeat left center;
          background-size: contain;
        }

        li {
            line-height: unset;
            padding: 0 !important;
            min-width: unset;
        }
    }
    .el-menu--inline {
      width: calc(100% - 2px)!important;
      margin-top: 0px!important;
      border-top: 0px!important;
      box-shadow: 0 2px 5px #ddd;
      >li {
        width: 90%;
        margin: 0 auto;
        &:last-child {
          >a {
            border-bottom: 0px!important;
          }
        }
        >a{
          background: #fff!important;
          border: 0px!important;
          b{
              color: #666666!important;
              font-size: 1.4rem!important;
              font-family: 'Domine-Regular', 'SourceHanSerifCN-Regular'!important;
              &:hover {
                color: @base_color!important;
              }
          }
        }
      }
    }
}
#menu .is-opened > .el-submenu__title{
    color:@base_color!important;
    .name{
        color:@base_color!important;
    }
}
#menu .is-opened > .el-submenu__title .el-submenu__icon-arrow{
    color:@base_color!important;
}
</style>

<style scoped lang="less">
  .ptxicon {
    width: 2.5rem;
    height: 2.5rem;
    background: url('/images/mobile/m_02.jpg') no-repeat center center;
    background-size: contain;
    display: inline-block;
    margin-left: .5rem;
  }
.search-box {
    width: 100%;
    display: inline-block;
    overflow: hidden;
    margin-bottom: 1rem;
    padding-top: .5rem;
    padding-bottom: .5rem;
  .search-input {
    width: 90%;
    margin: 0 auto;
    height: 3.5rem;
    background: #fff;
    border-radius: 5px;
    overflow: hidden;
    position: relative;
    box-shadow: 0 2px 5px #ddd;
    display: flex;
    align-items: center;
    > input {
        width: 100%;
        height: 100%;
        border: 0;
        padding-right: 4rem;
        box-sizing: border-box;
        font-size: 1.4rem;
        outline: none;
        padding-left: 1rem;
    }

    .searchBtn{
        width: 2.5rem;
        height: 2.5rem;
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
        margin-top: .5rem;
        padding-left: .5rem;
        img{
            width: 100%;
            margin: 0 auto;
            display: block;
        }
    }
  }
}
.header_logo {
    position: relative;
    width: 100%;
    height: 6rem;
    // padding-top: 1rem;
    // padding-bottom: 1rem;
    // border-bottom: 1px solid #fff;
    background-color: #3c408e;
    .Inner {
      width: 90%;
      margin: 0 auto;
      display: flex;
      justify-content: space-between;
      align-items: center;
      height: 6rem;
    }
    .left {
      width: 80%;
      // display: flex;
      // align-items: center;
      // justify-content: flex-start;
      .headerLang {
        /deep/ select {
           background: url(/images/pc/down.png) no-repeat 95% 10px;
           border: 1px solid #fff;
           color: #fff;
           font-size: 1.2rem;
           width: 7rem;
           padding-left: 1rem;
           height: 3rem;
           option {
            color: #333;
           }
        }
      }
    }
    .right {
      // width: 18%;
      display: flex;
      align-items: center;
      justify-content: flex-end;
      img{
        width: 1.8rem;
        display: block;
      }
    }
    .el-icon-close {
        color: #fff;
        font-size: 2.8rem;
    }

    a {
        width: 12rem;
    }

    .slide-menu {
        cursor: pointer;
    }
}

.innerShare {
  width: 90%;
  margin: 0 auto;
  display: flex;
  justify-content: center;
  a{
    width: 3.5rem;
    display: inline-block;
    margin-left: 1rem;
    img{
      width: 100%;
    }
  }
}
.login{
  display: flex;
  justify-content: center;
  position: relative;
  /deep/ .menberCentral{
    font-size: 1.4rem;
    color: #3c408e;
    border: 1px solid #3c408e;
    line-height: 2.4rem;
    padding: 0.5rem 2rem;
    border-radius: 2rem;
    margin: 0;
  }
}
</style>
