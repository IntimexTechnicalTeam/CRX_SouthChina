<template>
    <div class="CatMain NormalTop" :class="{'ENG' : $Storage.get('locale') === 'E'}">
      <div class="Normalcenter" v-if="catlayoutKey == 'OurService'">
        <transition name="slide">
          <div key="1" v-if="!waiting" style="display:flex;">
            <div class="Detailbanner">
              <img :src="BannerImg" v-if="BannerImg">
              <!-- <div class="TitleBg">
                <div class="innerBoxText">{{CateName}}</div>
              </div> -->

            </div>
          </div>
        </transition>
        <transition name="slide">
          <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
        </transition>
          <div class="NomralBg">
            <div class="NormalTitle">
                <span class="center">{{ CateName }}</span>
              </div>
              <!-- <p class="PathData"><router-link to="/" class="HomePath">{{$t('Message.HomeTips')}}</router-link><i class="el-icon-arrow-right"></i><span class="currentTitle">{{CateName}}</span></p> -->
              <div class="cms-list">
                  <div class="perData" v-for="(v,index) in ListData" :key="index">
                      <div class="left">
                          <p class="imgs" @click="GoLink(v)"><img :src="v.Cover.indexOf('.png')!==-1 || v.Cover.indexOf('.jpg')!==-1 || v.Cover.indexOf('.jpeg')!==-1 || v.Cover.indexOf('.gif')!==-1?v.Cover:NoImg"></p>
                      </div>
                      <div class="right">
                          <p class="title">{{v.Title}}</p>
                          <!-- <p class="contentTime">{{v.ContentDateTime}}</p> -->
                          <p class="desc">{{v.Desc}}</p>
                          <!-- <div class="HomeViewMore">
                              <router-link :to="'/cms/contentN/'+v.Id">{{$t('Message.LearnMore')}} >></router-link>
                          </div> -->
                      </div>
                  </div>
                  <div class="pager" v-if="totalRecord > pageSize">
                      <ins-page :total="totalRecord" v-model="currentPage" :pageNum="pageSize"></ins-page>
                  </div>
              </div>
          </div>
      </div>
      <div class="Normalcenter servicebox" v-else-if="catlayoutKey == 'Onlineaudit'">
          <div class="Detailbanner">
            <img :src="BannerImg" v-if="BannerImg">
          </div>
          <div class="NomralBg">
            <div class="NormalTitle">
                <span class="center">{{ CateName }}</span>
              </div>
              <div class="Content" v-html="servicecontent"></div>

            <div class="serviceMain fix">
              <ul class="right">
                <li>
                  <div class="Titleone">
                    {{$t('home.service')}}
                  </div>
                  <div class="textone">

                    {{$t('home.Price')}}
                  </div>
                </li>
                <li v-for="(v, index) in ListData" :key="index">
                  <div class="Title">
                    <p>{{ v.Title }}</p>
                  </div>
                  <div class="text">
                    <div class="Desc" v-html="v.Body">
                    </div>
                    <a class="btn" :href="'/CMS/content/'+v.Id">{{$t('home.Select')}}</a>
                  </div>
                </li>
              </ul>
            </div>
          </div>
      </div>
      <div v-else>
        <transition name="slide">
          <div key="1" v-if="!waiting" style="display:flex;">
            <div class="DetailTitle">
              <img :src="BannerImg" v-show="BannerImg!==null">
              <div class="TitleBg">
                <div class="innerBoxText">{{CateName}}</div>
              </div>
            </div>
          </div>
        </transition>
        <transition name="slide">
          <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
        </transition>
          <div class="NomralBg">
              <p class="PathData"><router-link to="/" class="HomePath">{{$t('Message.HomeTips')}}</router-link><i class="el-icon-arrow-right"></i><span class="currentTitle">{{CateName}}</span></p>
              <div class="cms-list">
                  <div class="perData" v-for="(v,index) in ListData" :key="index">
                      <div class="left">
                          <p class="imgs" @click="GoLink(v)"><img :src="v.Cover.indexOf('.png')!==-1 || v.Cover.indexOf('.jpg')!==-1 || v.Cover.indexOf('.jpeg')!==-1 || v.Cover.indexOf('.gif')!==-1?v.Cover:NoImg"></p>
                      </div>
                      <div class="right">
                          <p class="title">{{v.Title}}</p>
                          <p class="contentTime">{{v.ContentDateTime}}</p>
                          <p class="desc">{{v.Desc}}</p>
                          <div class="HomeViewMore">
                              <router-link :to="'/cms/contentN/'+v.Id">{{$t('Message.LearnMore')}} >></router-link>
                          </div>
                      </div>
                  </div>
                  <div class="pager" v-if="totalRecord > pageSize">
                      <ins-page :total="totalRecord" v-model="currentPage" :pageNum="pageSize"></ins-page>
                  </div>
              </div>
          </div>
      </div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component({
  components: {
    InsPage: () => import('@/components/base/pc/InsPage.vue')
  }
})
export default class InsCatLayout1 extends Vue {
    currentPage:number=1;
    pageSize:number=12;
    totalRecord:number=0;
    ListData:any[]=[];
    BannerImg:string='';
    CateName:string='';
    private SortOrder: string = 'desc';
    private SortName: string = 'CreateDate'
    private waiting: boolean = true;
    catlayoutID: string='';
    catlayoutKey: string='';
    servicecontent: string='';
    NoImg:string='/images/pc/proddef.jpg';
      get cid () {
      return this.$route.params.id;
    }
    GoLink(v) {
        window.location.href = '/cms/contentN/' + v.Id;
    }
    getContentsByCatId () {
        this.$Api.cms.getContentsByCatId(this.cid, this.currentPage, this.pageSize).then((result) => {
            if (result) {
                this.ListData = result.Data;
            }
            result.Data.forEach(function (i) {
            var newDate = new Date(i.ContentDateTime.replace(/-/g, '/'));
            i.ContentDateTime = newDate.getFullYear() + '-' + (newDate.getMonth() + 1) + '-' + newDate.getDate();
            });
            this.totalRecord = result.TotalRecord;
        });
    }
    getFromContentByCatId () {
      this.$Api.cms.getFromContentByCatId(this.cid, this.currentPage, this.pageSize, false, this.SortName, this.SortOrder).then((result) => {
        console.log(result, 'ddd');
        if (result) {
            this.ListData = result.Data;
        }
      }).catch((error) => {
        console.log(error, '错误');
      });
    }
   // 根据设备类型获取CMSCategory信息
  getCategoryByDevice () {
    this.$Api.cms.getCategoryByDevice({ CatId: this.cid, IsMobile: false }).then(async (result) => {
     console.log(result, 'gggggggg');
     this.BannerImg = result.ImagePath;
     this.CateName = result.Name;
     this.catlayoutID = result.Id;
     this.catlayoutKey = result.Key;
     this.servicecontent = result.Content;
      this.waiting = false;
    }).catch((error) => {
      console.log(error, 'error');
      this.$message({
        message: error,
        type: 'error'
      });
    });
  }
    created() {
        // this.getContentsByCatId();
        this.getFromContentByCatId();
        this.getCategoryByDevice();
    }
    @Watch('currentPage', { deep: true })
    onPagerChange() {
      // this.getContentsByCatId();
      this.getFromContentByCatId();
      this.getCategoryByDevice();
    }
}
</script>

<style scoped lang="less">
.pager {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
}
.faker {
    width: 100%;
    height: 30rem;
}
.CatMain {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
}
.Normalcenter{
  position: relative;
  width: 97%;
  min-width: 1200px;
  margin: 0 auto;
  .Detailbanner{

    img {
      width: 100%;
      min-height: calc(100vh - 204px);
      // min-height: 78vh;
      // vertical-align: bottom;
      display: inline-block;
      -o-object-fit: cover;
      object-fit: cover;
      -o-object-position: left;
      object-position: left;
    }
  }
  > .NomralBg{
    position: absolute;
    left: 10%;
    top: 10%;
  }
}
.NomralBg {
  margin-top: 1rem;
  .NormalTitle{
    justify-content: left;
    margin-bottom: 45px;
    width: auto;
    height: 40px;
    text-align: left;
    padding-top: 0;
    .center{
      position: relative;
      color: #333333;
      font-family: 'SourceHanSerifCN-Bold' !important;
      background-image: none;
      height: auto;
      padding-left: 24px;
      font-size: 36px;
      line-height: 40px;
      width: auto;
      padding-top: 0;
      &::before{
        content: '';
        width: 16px;
        height: 16px;
        background-color: #3c408e;
        position: absolute;
        left: 0;
        bottom: -24px;
        transform: rotateZ(45deg);
      }
      &::after{
        content: '';
        width: 234px;
        height: 2px;
        background-color: #3c408e;
        position: absolute;
        bottom: -17px;
        left: 16px;
        transform:translateX(0);
      }
    }
  }
}
.PathData {
  width: 1200px;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  margin-bottom: 1rem;
  span,a,i{
    font-size: 20px;
  }
  .HomePath {
    color: #666;
  }
  .currentTitle {
    color:#333333;
  }
}
.DetailTitle{
  width: 100%;
  display: flex;
  flex-wrap:wrap;
  position: relative;
  align-items: center;
  justify-content: center;
  img{
    width: 100%;
  }
  .TitleBg{
    width: 500px;
    border: 1px solid #ffffff;
    height: 70px;
    line-height: 70px;
    margin: 0 auto;
    padding: 10px;
    margin-bottom: 20px;
    top: 50%;
    position: absolute;
    transform: translateY(-50%);
    .innerBoxText{
      background:#ffffff;
      color: #333333;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 40px;
      font-weight: 700;
      font-family: 'Arial';
    }
  }
}
.cms-list {
    width: 710px;
    // margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
        .perData {
            width: 220px;
            height: 242px;
            margin-right: 20px;
            float: left;
            display: flex;
            flex-wrap: wrap;
            margin-bottom: 20px;
            cursor: pointer;
            position: relative;
            &:hover {
              .left {
                .imgs {
                  // border:1px solid @base_color;
                }
              }
            }
            &:nth-child(3n) {
              margin-right: 0px!important;
            }
            .left {
                width: 100%;
                .imgs {
                    // width: calc(100% - 2px);
                    display: block;
                    // border-radius: 5px;
                    overflow: hidden;
                    // border: 1px solid #eee;
                    transition: all .3s;
                    width: 100%;
                    img {
                        width: 100%;
                        display: block;
                        height: 242px;
                        object-fit: cover;
                        object-position: 50% 50%;
                    }
                }
            }
            .right {
                width: 100%;
                position: absolute;
                left: 50%;
                transform: translateX(-50%);
                bottom: 24px;
                text-align: center;
                .title {
                    font-size: 20px;
                    color: #fff;
                    text-overflow: -o-ellipsis-lastline;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    display: -webkit-box;
                    -webkit-line-clamp: 2;
                    line-clamp: 2;
                    -webkit-box-orient: vertical;
                    // margin-top: .5rem;
                    // margin-bottom: .5rem;
                    font-family: 'SourceHanSerifCN-Regular';
                    transition: all 0.3s;
                }

                .contentTime {
                    font-size: 18px;
                    color: #fff;
                    font-family: 'SourceHanSerifCN-Regular';
                }
                 .desc {
                    font-size: 18px;
                    color: #fff;
                    text-overflow: -o-ellipsis-lastline;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    display: -webkit-box;
                    -webkit-line-clamp: 4;
                    line-clamp: 4;
                    -webkit-box-orient: vertical;
                    // margin-top: .5rem;
                    // margin-bottom: .5rem;
                    font-family: 'SourceHanSerifCN-Regular';
                    transition: all 0.3s;
                }
                .HomeViewMore {
                    width: 100%;
                    display: flex;
                    flex-wrap: wrap;
                    justify-content: flex-start;
                    margin-top: 1rem;
                    a {
                        font-size: 18px;
                        color: #b19162;
                        border-bottom: 1px solid #b19162;
                    }
                }
                &:hover{
                  .title, .desc{
                    color: #3c408e;
                  }
                }
            }
        }
}
.catContent .servicebox{
  .NomralBg{
    width: 1200px;
    margin: 0 auto;
    left: 6%;
    padding-bottom: 0;
    // transform: translateX(-50%);
    .Content{
      margin-top: 36px;
      margin-bottom: 20px;
      /deep/ p{
        font-size: 24px;
        line-height: 32px;
        color: #333333;
        letter-spacing: 2px;
      }
    }
    .serviceMain{
      margin-top: 20px;
      border: 1px solid #b0b0b0;
      ul.left{
        width: 200px;
        float: left;
        border-right: 1px solid #cccccc;
        box-sizing: border-box;
        li:nth-child(1){
          height: 80px;
          background-color: #cecece;
          line-height: 80px;
          text-align: center;
          font-size: 24px;
          color: #333333;
        }
        li:nth-child(2){
          height: 150px;
          background-color: #f5f5f5;
          padding-top: 24px;
          text-align: center;
          font-size: 24px;
          color: #333333;
          box-sizing: border-box;
        }
      }
      ul.right {
        float: left;
      width: 1200px;
      margin: 0 auto;
      li {
        width: 200px;
        float: left;
        position: relative;
        transition: all 0.3s;
        &:first-child{
          border-right: 1px solid #cccccc;
          box-sizing: border-box;
        }
        .Titleone{
          line-height: 80px;
          text-align: center;
          font-size: 24px;
          color: #333333;
          // background-color: #cecece;
          background: url('/images/pc/serviceback.png');
        }
        .textone{
          height: 150px;
          background-color: #f5f5f5;
          padding-top: 24px;
          text-align: center;
          font-size: 24px;
          color: #333333;
          box-sizing: border-box;
        }
        .Title{
          height: 80px;
          // background-color: #cecece;
          background: url('/images/pc/serviceback.png');

          transition: all 0.3s;
          p{
            line-height: 80px;
          text-align: center;
          font-size: 24px;
          color: #333333;
          position: relative;
          z-index: 1;
          transition: all 0.3s;
          }
        }
        .text{
          height: 150px;
          background-color: #f5f5f5;

          text-align: center;
          transition: all 0.3s;
          .Desc{
            color: #333333;
            line-height: 24px;
            font-size: 20px;
            text-align: center;
            padding-top: 18px;
            z-index: 1;
            position: relative;
            transition: all 0.3s;
          }
          .btn{
            border-radius: 20px;
            border: 2px solid #3c408e;
            width: 102px;
            height: 40px;
            box-sizing: border-box;
            margin: 0 auto;
            font-size: 20px;
            color: #3c408e;
            line-height: 36px;
            margin-top: 15px;
            z-index: 1;
            position: relative;
            cursor: pointer;
            display: block;
          }
        }
        .Cover {
          width: 100%;
          img {
            width: 90%;
            margin: 0 auto;
            display: flex;
          }
        }
        &:hover{

          .Title::after{
            content: '';
            width: 200px;
            height: 80px;
            background-color: rgba(230, 199, 123, 0.6);
            position: absolute;
            top: 0;
            left: 0;
          }
          .text::after{
            content: '';
            width: 200px;
            height: 150px;
            background-color: rgba(230, 199, 123, 0.6);
            position: absolute;
            bottom: 0;
            left: 0;

          }
          .btn{
            background-color: #3c408e;
            color: #fff;
          }
          }
      }
    }
    }
  }
}
.ENG{
  .servicebox {
    .NomralBg .serviceMain {
      ul.right {
        li {
          .Title {
            display: flex;
            justify-content: center;
            align-items: center;
            p{
              line-height: 30px;
              word-break: break-word;
            }
          }
        }
      }
    }
  }
}
</style>
