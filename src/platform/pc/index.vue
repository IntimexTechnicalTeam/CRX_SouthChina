<template>
  <div>
    <Header v-show="routerPath!=='/building'" />
    <router-view></router-view>
    <Footer v-show="routerPath!=='/building'"/>
    <Sidebar />
    <div class="quote" v-if="routerPath!=='/'">
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
    bottom: 55px;
    width: 250px;
    height: 80px;
    background: url(/images/pc/arrow.png) 87% center #3c408e no-repeat;
    z-index: 100;
    span{
      display: block;
      width:78%;
      color: #fff;
      font-size: 24px;
      line-height: 80px;
      text-align: center;
      letter-spacing: 0.05em;
    }
  }
</style>
