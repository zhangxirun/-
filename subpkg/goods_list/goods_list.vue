<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
      <my-goods :goods="goods" ></my-goods>
      </view>
    </view>   
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //接收参数对象
        queryObj:{
          query:'',
          cid:'',
          pagenum:1,
          pagesize:10
        },
        goodsList:[],
        total:0,
        isloading:false
      }
    },
    onLoad(options) {
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      this.getGoodsList()
    },
    methods: {
      //获取商品列表数据的方法
     async getGoodsList(cb){
       // 开启节流阀
       this.isloading = true
       const {data:res} =  await uni.$http.get('/api/public/v1/goods/search',this.queryObj)
       // 关闭节流阀
       this.isloading = false
       // 数据请求完毕 立即调用cb回调函数
       cb && cb()
       if(res.meta.status !==200) return uni.$showMsg()
       
       this.goodsList = [...this.goodsList,...res.message.goods]
       this.total = res.message.total
     },
      onReachBottom(){
        // 判断是否还有下一页数据
        if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕')
        if(this.isloading) return
        // 让页码值自增加一
        this.queryObj.pagenum++
        this.getGoodsList()
      },
      onPullDownReach(){
        //重置数据
        this.queryObj.pagenum = 1
        this.goodsList = []
        this.total = 0
        this.isloading = false
        // 重新拉起请求
          this.getGoodsList(() => uni.stopPullDownRefresh())
      },
      gotoDetail(goods){
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id 
        })
      }
    },
  }
</script>

<style lang="scss">

</style>
