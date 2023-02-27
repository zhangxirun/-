<template>
  <view>
    <!-- 搜索栏 -->
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    <!-- 轮播图区域 -->
        <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
          <!-- 循环渲染轮播图的 item 项 -->
          <swiper-item v-for="(item, i) in swiperList" :key="i">
              <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
                <!-- 动态绑定图片的 src 属性 -->
                <image :src="item.image_src"></image>
              </navigator>
          </swiper-item>
        </swiper>
        <view class="navlist">
          <view v-for="(item,i) in navList" :key="i" class="nav-item" @click="navClickHandler(item)">
            <image :src="item.image_src" mode="" class="nav-img"></image>
          </view>
        </view>
        <!-- 楼层区域 -->
        <view class="floor-list">
          <!-- 楼层item项 -->
          <view class="floor-item" v-for="(item,i) in floorList" :key="i">
            <image :src="item.floor_title.image_src" class="floortitle"></image>
            <!-- 楼层的图片区域 -->
            <view class="floor-img-box">
              <!-- 左侧大图片区域 -->
              <navigator class="left-img-box" :url="item.product_list[0].url">
                <image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
              </navigator>
              <!-- 右侧四个图片区域 -->
              <view class="right-img-box">
                <navigator class="right-imgs" v-for="(item2,i2) in item.product_list " :key="i2" v-if="i2 !== 0" :url="item2.url">
                  <image :src="item2.image_src" mode="widthFix" :style="{width:item2.image_width + 'rpx'}"></image>
                </navigator>
              </view>
            </view>
          </view>
        </view>
  </view>
</template>

<script>
  import badgeMix from '@/mixins/tabbar-badge.js'

  export default {
    mixins:[badgeMix],
    data() {
      return {
        // 1 轮播图数据列表
        swiperList:[],
        // 分类导航数据
        navList:[],
        // 首页楼层数据
        floorList:[]
      };
    },
    onLoad() {
      // 获取数据
      this.getSwiperList(),
      this.getNavList(),
      this.getFloorList()
    },
    methods:{
      async getSwiperList(){
        const {data} =  await uni.$http.get('/api/public/v1/home/swiperdata')
        if(data.meta.status !== 200){
        return uni.$showMsg()
        }
        this.swiperList = data.message
      },
      async getNavList(){
        const { data } = await uni.$http.get('/api/public/v1/home/catitems')
        if(data.meta.status !== 200){
        return uni.$showMsg()
        }
        this.navList = data.message
      },
      navClickHandler(item){
        if(item.name === '分类'){
          uni.switchTab({
            url:'/pages/cate/cate'
          })
        }
        },
     async  getFloorList(){
       const {data} = await uni.$http.get('/api/public/v1/home/floordata')
       if(data.meta.status !== 200) return uni.$showMsg()
       // 对数据进行循环
      data.message.forEach( florr => {
        florr.product_list.forEach( prod => {
          prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
        })
      })
      this.floorList = data.message
        },
        gotoSearch(){
          uni.navigateTo({
            url:'/subpkg/search/search'
          })
        }
    }
  }
</script>

<style lang="scss">
  .search-box{
    // 设置效果为吸顶
    position: sticky;
    // 吸顶位置
    top: 0;
    // 提高层级防止被轮播图覆盖
    z-index: 999;
  }
  swiper {
   height: 330rpx;
  
   .swiper-item,
   image {
     width: 100%;
     height: 100%;
   }
  }
  .navlist{
    display: flex;
    justify-content: space-around;
    margin: 15rpx 0;
    .nav-img{
      width: 128rpx;
      height: 140rpx;
    }
  }
  .floortitle{
    width: 100%;
    height: 60rpx;
  }
  .right-img-box{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
 .floor-img-box{
   display: flex;
   padding-left: 10rpx;
   
 }
</style>
