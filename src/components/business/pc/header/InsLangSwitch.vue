<template>
    <div class="langSwitch">
        <p @click="changeLang('C')" :class="{'active': $Storage.get('locale') === 'C'}">繁體</p>
        <p @click="changeLang('E')" :class="{'active': $Storage.get('locale') === 'E'}">Eng</p>

      <!-- <p @click="changeLang(lang.value)" v-for="(lang,index) in FrontE.langList" :key="index" :class="{'active': $Storage.get('locale') === lang.value}">{{lang.name}}</p> -->
      <!-- <select  v-model="currentlang">
        <option :value="lang.value" v-for="(lang,index) in FrontE.langList" :key="index">{{lang.name}}</option>
      </select> -->
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Cookie from 'js-cookie';
@Component
export default class InsLangSwitch extends Vue {
  get currentlang () {
    return this.$i18n.locale;
  }
  set currentlang (val) {
    this.changeLang(val);
  }
  changeLang (lang) {
    this.$Api.member.setUILanguage(lang).then((result) => {
      this.$i18n.locale = lang;
      localStorage.setItem('locale', lang);
      this.Reload();
    }).catch((error) => {
      console.log(error);
    });
 }
}
</script>
<style scoped lang="less">
.langSwitch {
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 20px;
    line-height: 20px;
    p {
      display: flex;
      padding: 0 10px;
      // padding-top: 2px;
      cursor: pointer;
      position: relative;
      color: #fff;
      border: 1px solid #fff;
      border-radius: 3px;
      margin: 0 5px;
      font-size: 14px;
      height: 22px;
      line-height: 22px;
      box-sizing: border-box;
      align-items: center;
      &:last-child{
        margin-right: 0;
      }
    }
    .active {
      color: #999999;
      background-color: #fff;
    }
    select{
      width: 100%;
      background: transparent url('/images/mobile/m_10.jpg')  80% 50% no-repeat;
      background-size: 1rem;
      border:none;
      box-sizing: border-box;
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
</style>
