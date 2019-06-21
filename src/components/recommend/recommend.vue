<template>
   <v-touch v-on:swipeleft="onSwipeLeft" v-on:swiperight="swiperright" class="wrapper recommend">
            <scroll ref="scroll" class="recommend-content" :data="discList">
            <div>
                <div v-if="recomends.length" class="slider-wrapper">
                        <slider>
                            <div v-for="(item,index) in recomends" :key="index">
                                <a :href="item.linkUrl">
                                    <img class="needsclick" @load="loadImage" :src="item.picUrl">
                                </a>
                            </div>
                        </slider>
                    </div>
                    <div class="recommend-list">
                        <h1 class="list-title">热门歌单推荐</h1>
                        <ul>
                            <li class="item" v-for="(item,index) in discList" :key="index">
                                <div class="icon">
                                    <img width="60" height="60"  v-lazy="item.imgurl" alt="">
                                </div>
                                <div class="text">
                                    <h2 class="name" v-text="item.creator.name"></h2>
                                    <p class="desc" v-text="item.dissname"></p>
                                </div>
                            </li>
                        </ul>
                    </div>
            </div>
            <div class="loading-container" v-show="!discList.length">
                <loading></loading>
            </div>
            </scroll>
   </v-touch>
</template>
<script>
import Loading from 'base/loading/loading'
import Scroll from 'base/scroll/scroll'
import Slider from "base/slider/slider"
import { getRecommend, getDiscList } from "api/recommend"
import { ERR_OK } from "api/config";
export default {
  data() {
    return {
      recomends: [],
      discList: []
    };
  },
  created() {
      this._getRecommend();
      setTimeout(()=>{
        this._getDiscList();
      },1000)

  },
  methods: {
    onSwipeLeft () {
        this.$router.push('/singer')
    },
    swiperright() {
        this.$router.push('/search')
    },
    _getRecommend() {
      getRecommend().then(res => {
        console.log(res);
        if (res.code === ERR_OK) {
          this.recomends = res.data.slider;
        }
      });
    },
    _getDiscList(){
        getDiscList().then(res => {
            if (res.code === ERR_OK) {
                console.log(res.data.list)
                 this.discList = res.data.list
            }
        })
    },
    loadImage(){
        if(!this.checkedLoad){
            this.$refs.scroll.refresh()
            this.checkedLoad = true
        }
    }
  },
  components: {
    Slider,
    Scroll,
    Loading
  }
};
</script>
<style scoped lang="stylus" rel="stylesheet/stylus">
@import '~common/stylus/variable';

.recommend {
    position: fixed;
    width: 100%;
    top: 88px;
    bottom: 0;

    .recommend-content {
        height: 100%;
        overflow: hidden;

        .slider-wrapper {
            position: relative;
            width: 100%;
            overflow: hidden;
        }

        .recommend-list {
            .list-title {
                height: 65px;
                line-height: 65px;
                text-align: center;
                font-size: $font-size-medium;
                color: $color-theme;
            }

            .item {
                display: flex;
                box-sizing: border-box;
                align-items: center;
                padding: 0 20px 20px 20px;
                .icon {
                    flex: 0 0 60px;

                    padding-right: 20px;
                }
                 .text {
                    display: flex;
                    flex-direction: column;
                    justify-content: center;
                    flex: 1;
                    line-height: 20px;
                    overflow: hidden;
                    font-size: $font-size-medium;
                    .name {
                        margin-bottom: 10px;
                        color: $color-text;
                    }
                    .desc {
                        color: $color-text-d;
                    }
                }
            }
            .loading-container {
                position: absolute;
                width: 100%;
                top: 50%;
                transform: translateY(-50%);
            }

        }
    }
}
</style>