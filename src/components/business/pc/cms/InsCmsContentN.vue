<template>
  <div id="container" class="NomralBg NormalTop">
    <div class="CmsNormal">
      <div class="banner" v-if="BannerImg">
        <img :src="BannerImg" alt="">
      </div>
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
  catlayouID: string = '';
  BannerImg: string = '';
  get currentlang () {
    return this.$Storage.get('locale');
  }
  get id () {
    return this.$route.params.id ? this.$route.params.id : '';
  }
  getContent () {
    this.$Api.cms.getContentByDevice({ Key: this.id, ContentId: this.id, IsMobile: false }).then(result => {
      this.content = result.CMS;
      if (result.CMS.Title) document.title = result.CMS.Title;
      this.catlayouID = result.CMS.CatId;
      this.getCategoryByDevice(result.CMS.CatId);
    });
  }
  getCategoryByDevice(cateId) {
    this.$Api.cms.getCategoryByDevice({ CatId: cateId, IsMobile: false }).then(async (result) => {
     console.log(result, 'gggggggg');
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
.CmsNormal {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
  position: relative;
  .banner{
    width: 97%;
    margin: 0 auto;
    min-width: 1200px;
    display: block;
    img{
      width: 100%;
      display: block;
      min-height: calc(100vh - 170px);
      // min-height: 78vh;
      vertical-align: top;
      display: block;
      -o-object-fit: cover;
      object-fit: cover;
      -o-object-position: left;
      object-position: left;
    }
  }
  .CmsContent {
    width: 1200px;
    margin: 0 auto;
    // padding-top: 6rem;
    // padding-bottom: 6rem;
    min-height:500px;
    position: absolute;
    top: 80px;
    left: 50%;
    transform: translateX(-50%);
    .cmstext{
      /deep/p{
        &:nth-child(1){
          color: #221815;
          font-size: 24px;
          font-weight: bold;
          line-height: normal;
          margin-bottom: 6px;
        }
        color: #221815;
        font-size: 18px;
        line-height: 32px;
      }

      /deep/ table{
        tbody{
          tr{
            td{
              p:nth-child(1){
                color: #3c408e;
                font-size: 24px;
                font-weight: bold;
                line-height: normal;
                margin-bottom: 6px;
              }
              p{
                color: #221815;
                font-size: 18px;
                line-height: 32px;
              }
            }
          }
        }
      }

    }
    .textTitle {

      // display: flex;
      align-items: center;
      margin-bottom: 2rem;
      position: relative;
      &::before {
        content: '';
        height: 60px;
        width: 6px;
        background:#3c408e;
        display: inline-block;
        margin-right: 12px;
        position: absolute;
        left: 0;
      }
      p{
        font-size: 36px;
        color: #333333;
        line-height: 36px;
        margin-bottom: 8px;
        margin-left: 18px;
        font-family: 'SourceHanSerifCN-Bold';
      }
      span{
        color: #333333;
        font-size: 24px;
        margin-left: 18px;
        line-height: 6px;
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
