<template>
  <view>
  <view class="search-box">
    <!-- 使用 uni-ui 提供的搜索组件 -->
    <uni-search-bar  @input="input" :radius="100" cancelButton="none" placeholder="请输入搜索内容"></uni-search-bar>
  </view>
  <!-- 搜索建议列表 -->
  <view class="sugg-list" v-if="searchList.length !== 0">
    <view class="sugg-item" v-for="(item,i) in searchList" :key="i" @click="gotoDetail(item)">
      <view class="good-name">{{item.goods_name}}</view>
      <uni-icons type="arrowright" size="16"></uni-icons>
    </view>
  </view>
  <!-- 搜索历史 -->
  <view class="history-box" v-else>
    <!-- 标题区域 -->
    <view class="history-title">
      <text>搜索历史</text>
      <uni-icons type="trash-filled" size="17" @click="cleen"></uni-icons>
    </view>
    <!-- 列表区域 -->
    <view class="history-list">
      <uni-tag  type="default" :text="item" v-for="(item,i) in historys" :key="i" @click="gotoGoodList(item)"></uni-tag>
    </view>
  </view>
 
  
</view>
</template>

<script>
import searchVue from './search.vue';
  export default {
    data() {
      return {
         // 延时器的 timerId
            timer: null,
            // 搜索关键词
            va: '',
            // 搜索结果列表
            searchList:[],
            // 搜索历史
            historyList:['a','abc','qwe']
      };
    },
    computed: {
      historys(){
        return [...this.historyList].reverse()
      }
    },
    onLoad() {
    this.historyList = JSON.parse(uni.getStorageSync('va') || '[]')
    },
    methods: {
      input (value) {
        // 清除 timer 对应的延时器
          if(this.timer) clearTimeout(this.timer)
          // 重新启动一个延时器，并把 timerId 赋值给 this.timer
          this.timer = setTimeout(() => {
            // 如果 500 毫秒内，没有触发新的输入事件，则为搜索关键词赋值
            this.va = value
            this.getSearchList()
            this.saveSearchHistory()
            console.log(this.va)
          }, 1000)
      },
     async getSearchList(){
       // 判断关键词是否为空
       if(this.va === ''){
         this.searchList = []
         return
       }
       const { data:{message,meta} } = await uni.$http.get('/api/public/v1/goods/qsearch',{query:this.va})
       if(meta.status !== 200) return uni.$showMsg()
       this.searchList = message
       
     },
     gotoDetail(item){
       uni.navigateTo({
         url:'/subpkg/goods_detail/goods_detail?goods_id' + item.goods_id
       })
     },
     saveSearchHistory(){
       // this.historyList.push(this.va)
       // 转化为set对象
       const set = new Set(this.historyList)
       //  调用方法去重
       set.delete(this.va)
       // 重新添加
       set.add(this.va)
       // 将set对象转化成数组
       this.historyList = Array.from(set)
       // 搜索历史数据持久化
       uni.setStorageSync('va',JSON.stringify(this.historyList))
     },
     cleen(){
       this.historyList = []
       uni.setStorageSync('va','[]')
     },
     gotoGoodList(va){
       uni.navigateTo({
         url:'/subpkg/goods_list/goods_list?query=' + va
       })
     }
    }   
  }
</script>

<style lang="scss">
  .history-box{
    padding: 0 5px;
    .history-title{
      display: flex;
      justify-content: space-between;
      height: 40px;
      align-items: center;
      font-size: 13px;
      border-bottom: 1px solid #efefef;
    }
    .history-list{
      display: flex;
      flex-wrap: wrap;
      .uni-tag{
        margin-right: 5px;
        margin-top: 5px;
      }
    }
  }
  .sugg-list{
    padding: 0 5px;
    .sugg-item{
      display: flex;
      align-items: center;
      justify-content: space-around;
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;
      .good-name{
        // 超出允许换行
        white-space: nowrap;
        overflow: hidden;
        // 隐藏部分用...表示
        text-overflow: ellipsis;
        
      }
    }
  }
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
  }
</style>
