<template>
  <div class="main_warpper">
    <ins-header  v-show="routerPath!=='/building'" />
    <router-view></router-view>
    <ins-footer  v-show="routerPath!=='/building'" />
    <ins-sidebar  />
    <ins-slide-menu :direction="'right'">
      <ins-menu-layout />
    </ins-slide-menu>
    <div class="quote" v-if="routerPath!=='/'" :class="{'ENG' : $Storage.get('locale') === 'E'}">
      <router-link to="/cms/catDetail/40119">
        <span>{{$t('home.Quote')}}</span>
      </router-link>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import '../../rem';
import Cookie from 'js-cookie';
@Component({
  components: {
    InsHeader: () => import('@/components/business/mobile/header/InsHeader.vue'),
    InsFooter: () => import('@/components/business/mobile/footer/InsFooter.vue'),
    InsSlideMenu: () => import('@/components/business/mobile/header/InsSlideMenu.vue'),
    InsMenuLayout: () => import('@/components/hkTasteBusiness/mobile/header/InsMenuLayout.vue'),
    InsSidebar: () => import('@/components/business/pc/header/InsSidebar.vue')
  }
})
export default class mobileIndex extends Vue {
  created () {
    Vue.prototype.vw = window.innerWidth;
    this.changLange(this.$Storage.get('locale') || 'E');
  }
  get routerPath() {
    return this.$route.path;
  }
  private changLange (lang) {
    this.$Api.member.setUILanguage(lang).then((result) => {
      this.$i18n.locale = lang;
      this.$Storage.set('locale', lang);
    }).catch((error) => {
      console.log(error);
    });
  }
}
</script>
<style lang="less" scoped>
.main_warpper{
  background:#FFF;
  position: relative;
}
.quote{
    position: absolute;
    left: 3%;
    bottom: 6rem;
    width: 15rem;
    height: 4.5rem;
    background: url(/images/pc/arrow.png) 90% center #3c408e no-repeat;
    z-index: 100;
    background-size: 1.5rem;
    span{
      display: block;
      width:84%;
      color: #fff;
      font-size: 1.6rem;
      line-height: 4.5rem;
      text-align: center;
      // letter-spacing: 0.05em;
    }
  }
.quote.ENG{
  background: url(/images/pc/arrow.png) 93% center #3c408e no-repeat;
  span{
    font-size: 1.4rem;

  }
}
</style>
