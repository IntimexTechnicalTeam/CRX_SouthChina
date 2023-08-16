<template>
    <div class="header-box">
        <div class="flex-box">
          <ins-logo />
          <img class="slide-menu" src="/images/mobile/m_64.png" @click="showSlideMenu" v-if="menuShow" />
          <img class="slide-menu" src="/images/mobile/m_06.png" @click="showSlideMenu" v-else />
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

@Component({
  components: {
    InsLogo: () => import('@/components/base/mobile/InsLogo.vue'),
    InsMenu: () => import('@/components/business/mobile/header/InsMenu.vue')
  }
})
export default class DefaultHeader extends Vue {
  @Prop() private showInFixed!: boolean;

  showSlideMenu () {
    let isShow = !JSON.parse(JSON.stringify(this.menuShow));
    this.$store.dispatch('isShowMenu', isShow);
  }

  get menuShow () {
    return this.$store.state.isShowMenu;
  }

  get isMobile () {
    return this.$store.state.isMobile;
  }
  get ShopCart () {
    return this.$store.state.shopCart;
  }
  created() {
    this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
  }
    @Watch('$route', { deep: true })
    onRouteChange (n, o) {
          this.$store.dispatch('isShowMenu', false);
    }
}
</script>

<style scoped lang="less">
    .header-box {
      .flex-box {
        height: 6rem;
        background-color: @positive_color;
        position: relative;
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 1.5rem;
        border-bottom: 2px solid #666666;

        .logo {
          width: 15rem;
        }

        .slide-menu {
          cursor: pointer;
          width: 3rem;
        }
        .close-meun {
          cursor: pointer;
          width: 2.5rem;
        }
      }
    }
.headerTop{
  width: 100%;
  display: flex;
  background: #fff;
  position: relative;
  border-bottom: 1px solid #e6e6e6;
  flex-wrap: wrap;
  .shoppingcart{
      padding-left: 1rem;
      padding-right: 1rem;
      float: left;
      height: 3.5rem;
      border-right: 1px solid #e6e6e6;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  .headerLang{
    width:20%;
    border-left: 1px solid #e6e6e6;
    position: absolute;
    right: 0px;
    height: 3.5rem;
    padding-left: 1rem;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .header-code{
    width:20%;
    border-left: 1px solid #e6e6e6;
    position: absolute;
    right: 25%;
    height: 3.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  /deep/ .langSwitch{
    select{
      width: 100%;
      background: transparent url('/images/mobile/arrow-down-back.png')  80% 50% no-repeat;
      background-size: 1rem;
      border:none;
      box-sizing: border-box;
      text-align: center;
      color:#666666;
      appearance: none;
      -moz-appearance: none;
      -webkit-appearance: none;
      padding-left: .8rem;
      font-size: 1.4rem;
      outline: none;
      option{
        color:#666666;
        outline: none;
      }
    }
  }
}
</style>
