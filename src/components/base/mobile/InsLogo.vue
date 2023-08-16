<template>
  <a href="/" class="logo">
    <img src="/images/mobile/M_logo.png" />
  </a>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component
export default class InsLogo extends Vue {

}
</script>
<style scoped lang="less">
.logo {
  img {
    width: 100%;
  }
}
</style>
