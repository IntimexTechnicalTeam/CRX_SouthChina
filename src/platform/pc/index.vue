<template>
  <div>
    <Header v-show="routerPath!=='/building'" />
    <router-view></router-view>
    <Footer v-show="routerPath!=='/building'"/>
    <Sidebar />
    <div class="quote" :class="{'ENG' : $Storage.get('locale') === 'E'}" v-if="routerPath!=='/'">
      <router-link to="/cms/catDetail/40119">
        <span>{{$t('home.Quote')}}</span>
      </router-link>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
@Component({
  components: {
    Header: () => import(/* webpackChunkName: "main" */ '@/components/business/pc/header/InsHeader.vue'),
    Footer: () => import(/* webpackChunkName: "main" */ '@/components/business/pc/footer/InsFooter.vue'),
    Sidebar: () => import(/* webpackChunkName: "main" */ '@/components/business/pc/header/InsSidebar.vue')
  }
})
export default class pcIndex extends Vue {
  get routerPath() {
    return this.$route.path;
  }
}
</script>

<style lang="less" scoped>
.quote{
    position: absolute;
    left: 1.5%;
    bottom: 0;
    width: 250px;
    height: 80px;
    background: url(/images/pc/arrow.png) 87% center #3c408e no-repeat;
    z-index: 100;
    span{
      display: flex;
      align-items: center;
      width:78%;
      color: #fff;
      font-size: 24px;
      // height: 80px;
      line-height: 80px;
      text-align: center;
      letter-spacing: 0.05em;
      padding-left: 15px;
      box-sizing: border-box;
    }
    &.ENG{
      background: url(/images/pc/arrow.png) 92% center #3c408e no-repeat;
      span{
        width:80%;
        color: #fff;
        font-size: 22px;
        height: 80px;
        padding-left: 10px;
        box-sizing: border-box;
      }
    }
  }
</style>
