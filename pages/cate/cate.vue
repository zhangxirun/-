<template>
  <view>
    <!-- 搜索栏 -->
    <!-- <my-search :bgcolor="'red'" :radius="10"></my-search> -->
    <my-search @click="gotoSearch"></my-search>
    <view class="scroll-view-container">
      <!-- 左侧的滑动区域 -->
      <scroll-view scroll-y="true" :style="{height: wh + 'px'}" class="left-scroll">
        <block v-for="(item,i) in cateList" :key="i">
          <view :class="['scroll-left-item',i === active ? 'active' : '']" @click="activeChanged(i)">{{item.cat_name}}</view>
        </block>
      </scroll-view>
      <!-- 右侧滑动区域 -->
      <scroll-view scroll-y="true" :style="{height: wh + 'px'}" :scroll-top="scrollTop" >
        <view class="right-item" v-for="(item2,i2) in cateList2" :key="i2">
          <view style="margin-bottom: 20px;">/{{item2.cat_name}}/</view>
          <!-- 二级分类下的三级列表 -->
          <view class="cate-live3-list">
            <!-- 三级分类item项 -->
            <view class="cate-live3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="gotoList(item3)">
              <!-- 三级分类图片 -->
              <image :src="item3.cat_icon" ></image>
              <!-- 三级分类文字 -->
              <text>{{item3.cat_name}}</text>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  import badgeMix from '@/mixins/tabbar-badge.js'

  export default {
    mixins:[badgeMix],
    data() {
      return {
        // 定义一个窗口高度数据
        wh:0, 
        // 分类列表
        cateList:[],
        active:0,
        // 二级分类列表
        cateList2:[],
        // 滚动条位置
        scrollTop:0
      }
    },
    
    methods: {
      // 获取分类列表的数据
      async getCateList(){
      const {data} =  await uni.$http.get('/api/public/v1/categories')
      if(data.meta.status !== 200) return uni.$showMsg()
      this.cateList = data.message
      // 二级列表分类
      this.cateList2 =  data.message[0].children
      },
      activeChanged(i){
        this.active = i
        // 重新为二级分类赋值
        this.cateList2 =  this.cateList[i].children
        // 重新为滚动条赋值
        this.scrollTop = this.scrollTop === 0 ? 1 : 0
        // :scroll-top="scrollTop"
      },
      gotoList(item3){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?cid=' + item3.cat_id
        })
      },
      gotoSearch(){
        uni.navigateTo({
          url:'/subpkg/search/search'
        })
      }
    },
    onLoad() {
     const sync = uni.getSystemInfoSync()
      this.wh = sync.windowHeight - 50
      this.getCateList()
    },
  }
</script>

<style lang="scss">
 .cate-live3-list{
   display: flex;
   flex-wrap: wrap;
   .cate-live3-item{
     width: 33.33%;
     display: flex;
     flex-direction: column;
     justify-content: center;
     align-items: center;
     margin-bottom: 10px;
    image{
      width: 60px;
      height: 60px;
    }
    text{
      font-size: 12px;
    }
   }
 }
  .right-item{
    font-size: 12px;
    text-align: center;
    padding: 15px 0;
    
    font-weight: bold;
  }
  .scroll-view-container{
    display: flex;
  }
  .left-scroll{
    width: 120px;
  }
  .scroll-left-item{
    background-color: #F7F7F7;
    line-height: 60px;
    text-align: center;
    font-size: 12px; 
    
    &.active{
      background-color: #FFFFFF;
      position: relative;
      
      &::before{
        content: '';
        display: block;
        width: 3px;
        height: 30px;
        background-color: #C00000;
        position: absolute;
        top: 50%;
        left:0;
        transform: translateY(-50%);
      }
    }
  }
  
</style>
