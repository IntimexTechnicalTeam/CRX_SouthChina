<template>
    <div class="CatMain NoramlPaddingTop">
      <div class="Normalcenter" v-if="catlayoutKey == 'OurService'">
        <!-- <transition name="slide"> -->
          <!-- <div key="1" v-if="!waiting" style="display:flex;"> -->
            <div class="DetailTitle">
              <img :src="BannerImg" v-if="BannerImg">
            </div>
          <!-- </div> -->
        <!-- </transition> -->
        <!-- <transition name="slide">
          <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
        </transition> -->
          <div class="NomralBg">
            <div class="NormalTitle">
                <span class="center">{{ CateName }}</span>
            </div>
            <div class="cms-list">
                <div class="perData" v-for="(v,index) in ListData" :key="index">
                    <div class="list_img">
                        <p class="imgs" @click="GoLink(v)"><img :src="v.Cover.indexOf('.png')!==-1 || v.Cover.indexOf('.jpg')!==-1 || v.Cover.indexOf('.jpeg')!==-1 || v.Cover.indexOf('.gif')!==-1?v.Cover:NoImg"></p>
                    </div>
                    <div class="list_text">
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
        <div class="DetailTitle">
          <img :src="BannerImg" v-if="BannerImg">
        </div>
        <div class="NomralBg">
            <div class="NormalTitle">
                <span class="center">{{ CateName }}</span>
              </div>
              <div class="Content" v-html="servicecontent"></div>

            <div class="serviceMain fix">
              <div class="top fix">
                <div class="Titleone">
                    {{$t('home.service')}}
                  </div>
                  <div class="textone">

                    {{$t('home.Price')}}
                  </div>
              </div>
              <ul class="bottom">
                <li class="fix" v-for="(v, index) in ListData" :key="index">
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
              <!-- <p class="PathData"><router-link to="/" class="HomePath">{{$t('Message.HomeTips')}}</router-link><i class="el-icon-arrow-right"></i><span class="currentTitle">{{CateName}}</span></p> -->
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
    InsPage: () => import('@/components/base/mobile/InsPage.vue')
  }
})
export default class InsCatLayout1 extends Vue {
    currentPage:number=1;
    pageSize:number=6;
    totalRecord:number=0;
    ListData:any[]=[];
    BannerImg:string='';
    CateName:string='';
    catlayoutID: string='';
    catlayoutKey: string = '';
    servicecontent: string='';
    private waiting: boolean = true;
    private SortOrder: string = 'asc';
    private SortName: string = 'CreateDate'
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
   // 根据设备类型获取CMSCategory信息
  getCategoryByDevice () {
    this.$Api.cms.getCategoryByDevice({ CatId: this.cid, IsMobile: true }).then(async (result) => {
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
  getFromContentByCatId () {
      this.$Api.cms.getFromContentByCatId(this.cid, this.currentPage, this.pageSize, true, this.SortName, this.SortOrder).then((result) => {
        console.log(result, 'ddd');
        if (result) {
            this.ListData = result.Data;
        }
      }).catch((error) => {
        console.log(error, '错误');
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
      this.getCategoryByDevice();
      this.getFromContentByCatId();
    }
}
</script>

<style scoped lang="less">
.faker {
    width: 100%;
    height: 30rem;
    text-align: center;
}
.CatMain {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
}
.PathData {
  width: 90%;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  margin-bottom: 1rem;
  margin-top: 1rem;
  span,a,i{
    font-size: 1.4rem;
  }
  .HomePath {
    color: #666;
  }
  .currentTitle {
    color:#9f1e3c;
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
      justify-content: left;
    margin-bottom: 3rem;
    width: auto;
    height: 3rem;
    text-align: left;
    padding-top: 3rem;
    padding-left: 2rem;
    .innerBoxText{
      position: relative;
      color: #333333;
      font-family: 'SourceHanSerifCN-Bold' !important;
      background-image: none;
      height: auto;
      padding-left: 1.5rem;
      font-size: 2rem;
      line-height: 3rem;
      width: auto;
      padding-top: 0;
      &::before{
        content: '';
        width: 1rem;
        height: 1rem;
        background-color: #3c408e;
        position: absolute;
        left: 0;
        bottom: -18px;
        transform: rotateZ(45deg);
      }
      &::after{
        content: '';
        width: 12rem;
        height: 2px;
        background-color: #3c408e;
        position: absolute;
        bottom: -13px;
        left: 1rem;
        transform:translateX(0);
      }
    }
    }
  }
.cms-list {
    width: 90%;
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
        .perData {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            border-bottom: 1px solid #e6e6e6;
            padding-bottom: 2rem;
            padding-top: 2rem;
            .left {
                width: 40%;
                .imgs {
                    width: 100%;
                    display: inline-block;
                    img {
                        width: 100%;
                    }
                }
            }
            .right {
                width: 55%;
                .title {
                    font-size: 1.2rem;
                    color: #9f1e3c;
                    text-overflow: -o-ellipsis-lastline;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    display: -webkit-box;
                    -webkit-line-clamp: 2;
                    line-clamp: 2;
                    -webkit-box-orient: vertical;
                    margin-top: .5rem;
                    margin-bottom: .5rem;
                }
                .contentTime {
                    font-size: 1.2rem;
                    color: #999999;

                }
                 .desc {
                    font-size: 1.2rem;
                    color: #333333;
                    text-overflow: -o-ellipsis-lastline;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    display: -webkit-box;
                    -webkit-line-clamp: 4;
                    line-clamp: 4;
                    -webkit-box-orient: vertical;
                    margin-top: .5rem;
                    margin-bottom: .5rem;
                }
                .HomeViewMore {
                    width: 100%;
                    display: flex;
                    flex-wrap: wrap;
                    justify-content: flex-end;
                    margin-top: 2rem;
                    a {
                        font-size: 1.2rem;
                        color: #b19162;
                        border-bottom: 1px solid #b19162;
                    }
                }
            }
        }
}
.NoramlPaddingTop{
  padding-bottom: 0;
}
.Normalcenter{
  width: 94%;
  margin: 1rem auto;
  margin-bottom: 0;
  position: relative;
  .NomralBg{
    position: absolute;
    top: 3rem;
    left: 0;
    padding-bottom: 0;
  }
  .NormalTitle{
    justify-content: left;
    margin-bottom: 3rem;
    width: auto;
    height: 3rem;
    text-align: left;
    padding-top: 0;
    padding-left: 2rem;
    .center{
      position: relative;
      color: #333333;
      font-family: 'SourceHanSerifCN-Bold' !important;
      background-image: none;
      height: auto;
      padding-left: 1.5rem;
      font-size: 2rem;
      line-height: 3rem;
      width: auto;
      padding-top: 0;
      &::before{
        content: '';
        width: 1rem;
        height: 1rem;
        background-color: #3c408e;
        position: absolute;
        left: 0;
        bottom: -18px;
        transform: rotateZ(45deg);
      }
      &::after{
        content: '';
        width: 12rem;
        height: 2px;
        background-color: #3c408e;
        position: absolute;
        bottom: -13px;
        left: 1rem;
        transform:translateX(0);
      }
    }
  }
  .cms-list {
    margin-top: 4rem;
    .perData {
      width: 48%;
      border-bottom: none;
      position: relative;
      margin-right: 4%;
      display: block;
      padding: 0;
      margin-bottom: 4%;
      height: 14rem;
      overflow: hidden;
      &:nth-child(2n) {
        margin-right:0 ;
      }
      .list_img{
        width: 100%;
        .imgs{
          display: block;
          img{
            width: 100%;
            display: block;
            object-fit: cover;
            object-position: center;
            height: 14rem;
          }
        }
      }
      .list_text{
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        bottom: 1rem;
        width: 100%;
        text-align: center;
        .title, .desc{
          font-size: 1.2rem;
          color: #ffffff;
        }
        .desc{
          margin-top: 0.3rem;
        }
      }
    }
  }
}
.Normalcenter.servicebox{
  .NormalTitle .center{
    &::after{
      width: 18rem;
    }
  }
  .Content{
    width: 90%;
    margin: 0 auto;
    margin-top: 4rem;
    /deep/ p{
      font-size: 1.3rem;
      line-height: 2rem;
      color: #333333;
      text-align: justify;
    }
  }
  .serviceMain{
      width: 90%;
      margin: 0 auto;
      border: 1px solid #cccccc;
      margin-top: 3rem;
      .top{
        .Titleone{
          width: 30%;
          height: 4.5rem;
          line-height: 4.5rem;
          // background-color: #d0d0d0;
          float: left;
          text-align: center;
          font-size: 1.3rem;
          color: #333333;
          background: url('/images/mobile/serviceback.png') right center;
        }
        .textone{
          width: 70%;
          height: 4.5rem;
          line-height: 4.5rem;
          background-color: #f5f5f5;
          float: left;
          font-size: 1.3rem;
          padding-left: 3.7rem;
          box-sizing: border-box;
          color: #333333;
        }
      }
      ul.bottom{
        li{
          border-top: 1px solid #cccccc;
          .Title{
            width: 30%;
            height: 6rem;
            // line-height: 4rem;
            // background-color: #d0d0d0;
            background: url('/images/mobile/serviceback.png') right center;
            float: left;
            text-align: center;
            display: flex;
            justify-content: center;
            align-items: center;
            p{
              font-size: 1.3rem;
              color: #333333;
              word-break: break-word;
            }
          }
          .text{
            width: 70%;
            height: 6rem;
            // line-height: 4rem;
            background-color: #f5f5f5;
            float: left;
            // padding-left: 3rem;
            box-sizing: border-box;
            display: flex;
            align-items: center;
            .Desc{

              width: 55%;
              float: left;
              /deep/ p{
                font-size: 1.3rem;
                color: #333333;
                text-align: center;
              }
            }
            .btn{
              border-radius: 2rem;
              border: 2px solid #3c408e;
              width: 7rem;
              height: 3rem;
              box-sizing: border-box;
              margin: 0 auto;
              font-size: 1.3rem;
              color: #3c408e;
              line-height: 2.6rem;
              z-index: 1;
              position: relative;
              cursor: pointer;
              float: left;
              text-align: center;
            }
          }
        }
      }
    }
}
</style>
