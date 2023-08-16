<template>
  <div class="main-code">
    <select id="code-select" v-model="codeId" @change="changeCurrency(codeId)">
      <option value="1">HKD</option>
      <option value="2">USD</option>
      <option value="3">RMB</option>
    </select>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component
export default class InsCodeSelect extends Vue {
  private codeId: number = 1;
  changeCurrency (val) {
    this.$Api.member.setCurrency(val).then((result) => {
      this.$message({
        message: this.$t('changeCurSuccess') as string,
        type: 'success'
      });

      this.$Storage.set('currency', result.Currency.Id);
      this.Reload();
    }).catch((error) => {
      console.log(error);
    });
  }

  created() {
    this.codeId = this.$Storage.get('currency');
  }

  // LoadData () {
  //   this.$Api.shoppingCart.LoadData().then((result) => {
  //     this.codeId = result.Currency.Id;
  //   });
  // }
  mounted () {
    // this.LoadData();
  }
}
</script>
<style lang="less">
.messageboxNoraml{
  z-index: 1000000000!important;
}
</style>
<style scoped lang="less">
.main-code {
  display: flex;
  width: 6rem;
  align-items: center;
  justify-content: center;
  color: #999999;
  margin-left: 1rem;
  select {
      width: 100%;
      background: transparent url('/images/mobile/m_10.jpg')  90% 5px no-repeat;
      background-size: 1.5rem;
      border:none;
      box-sizing: border-box;
      color: #999999;
      appearance: none;
      -moz-appearance: none;
      -webkit-appearance: none;
      padding-left: .8rem;
      font-size: 1.4rem;
      outline: none;
      height: 2.5rem;
      option{
        color:#666666;
        outline: none;
      }
  }
}
</style>
