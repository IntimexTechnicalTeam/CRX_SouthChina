<template>
  <div id="container" class="NomralBg">
    <div class="CmsNormal" :style="'background: #eeeeee url('+BannerImg+') no-repeat right bottom;'">
      <!-- :style="'background: #eeeeee url('+BannerImg+') no-repeat right bottom;'" -->
      <!-- <div class="banner" v-if="BannerImg">
        <img :src="BannerImg" alt="">
      </div> -->
      <div class="CmsContent">
          <div class="textTitle">
            <p>{{content.Title}}</p>
            <span v-if="content.Desc">
              {{content.Desc}}
            </span>
          </div>
          <div class="cmstext" v-html="content.Body"></div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import { FrontE } from '@/sdk/common/SysConst';
import Cookie from 'js-cookie';
@Component({
  components: {
  }
})
export default class InsCmsContentN extends Vue {
  content: any[] = [];
  BannerImg: string = '';
  get currentlang () {
    return this.$Storage.get('locale');
  }
  get id () {
    return this.$route.params.id ? this.$route.params.id : '';
  }
  getContent () {
    this.$Api.cms.getContentByDevice({ Key: this.id, ContentId: this.id, IsMobile: true }).then(result => {
      this.content = result.CMS;
      if (result.CMS.Title) document.title = result.CMS.Title;
      this.getCategoryByDevice(result.CMS.CatId);
    });
  }
  getCategoryByDevice(cateId) {
    this.$Api.cms.getCategoryByDevice({ CatId: cateId, IsMobile: true }).then(async (result) => {
     this.BannerImg = result.ImagePath;
    }).catch((error) => {
      console.log(error, 'error');
      this.$message({
        message: error,
        type: 'error'
      });
    });
  }
  get lang () {
    return this.$Storage.get('locale');
  }
  get queryLang () {
    return this.$route.query.Lang || '';
  }
  created () {
    this.getContent();
  }
  @Watch('$route', { deep: true })
  onIdChange () {
    this.getContent();
  }
}
</script>
<style scoped lang="less">
.clear {
  clear: both;
}
.NomralBg{
  padding-bottom: 0;
}
.CmsNormal {
  width: 94%;
  margin: 1rem auto;
  margin-bottom: 0;
  display: flex;
  flex-wrap: wrap;
  position: relative;
  background-position: bottom !important;
  background-size: contain !important;
  background-color: #eeeeee;
  padding-top: 3rem;
  padding-bottom: 40rem;
  height: 100vh;
  .banner{
    width: 100%;
    display: flex;
    align-items: self-end;
    img{
      width: 100%;
      display: block;
    }
  }
  .CmsContent {
    width: 100%;
    margin: 0 auto;
    // padding-top: 6rem;
    // padding-bottom: 6rem;
    // min-height: 15rem;
    // position: absolute;
    top: 3rem;
    left: 0;
    padding-left: 1.5rem;
    padding-right: 1.5rem;
    box-sizing: border-box;
    .textTitle {

      // display: flex;
      align-items: center;
      margin-bottom: 2rem;
      position: relative;
      &::before {
        content: '';
        height: 4rem;
        width: 6px;
        background:#3c408e;
        display: inline-block;
        margin-right: 12px;
        position: absolute;
        left: 0;
      }
      p{
        font-size: 2rem;
        color: #333333;
        line-height: 2.4rem;
        margin-bottom: 8px;
        margin-left: 1.2rem;
        font-family: 'SourceHanSerifCN-Bold';
      }
      span{
        color: #333333;
        font-size: 1.4rem;
        margin-left: 1.2rem;
        line-height: 6px;
      }

    }
    .cmstext{
      // /deep/p{
      //   &:nth-child(1){
      //     color: #221815;
      //     font-size: 1.6rem;
      //     font-weight: bold;
      //     line-height: normal;
      //     margin-bottom: 0.5rem !important;
      //   }
      //   color: #221815;
      //   font-size: 1.2rem;
      //   line-height: 2.4rem;
      // }

      /deep/ table{
        tbody{
          tr{
            td{
              p:nth-child(1){
                color: #3c408e;
                font-size: 1.6rem;
                font-weight: bold;
                line-height: normal;
                margin-bottom: 0.5rem !important;
              }
              p{
                color: #221815;
                font-size: 1.4rem;
                line-height: 2.4rem;
              }
            }
          }
        }
      }

    }
    /deep/ p {
      font-size: 1.2rem;
      line-height: 2rem;
      color: #666666;
      word-break: break-word;
    }
    /deep/ strong {
      font-size: 1.2rem;
      line-height: 2rem;
      color: #666666;
      font-weight: 700;
      word-break: break-word;
    }
    /deep/ img {
      max-width: 100%;
    }
  }
}
</style>
