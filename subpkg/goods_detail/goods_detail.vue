<template>
  <view v-if="goods_info.goods_name" class="goods-container">
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
      <swiper-item v-for="(item,i) in goods_info.pics" :key="i">
        <image :src="item.pics_big" @click="preview(i)" ></image>
      </swiper-item>
    </swiper>
    <!-- 商品信息区域 -->
    <view class="goods-info-box">
      <!-- 商品价格 -->
      <view class="price">￥{{goods_info.goods_price}}</view>
      <!--  商品信息主体区域 -->
      <view class="goods-item-body">
        <view class="goods-name">{{goods_info.goods_name}}</view>
        <view class="goods-favi">
          <uni-icons type="star" size="30" color="gray"></uni-icons>
          <text>收藏</text>
        </view>
      </view>
      <!-- 运费 -->
      <view class="yf">运费:免运费</view>
    </view>
    <rich-text :nodes="goods_info.goods_introduce"></rich-text>
    <!-- 商品导航 -->
     <view class="goods_nav">
       <uni-goods-nav :fill="true"  :options="options" :buttonGroup="buttonGroup"  @click="onClick" @buttonClick="buttonClick" />
     </view>
  </view>
</template>

<script>
  import { mapState,mapMutations,mapGetters} from 'vuex'
  export default {
    computed:{
      ...mapState('m_cart',[]),
      ...mapGetters('m_cart',['total'])
    },
    watch:{
      total:{
      handler(newVal){
        const findResult = this.options.find(x => x.text === '购物车')
        if(findResult){
          findResult.info = newVal
        }
      },
      immediate:true
    }
    },
    data() {
      return {
        // 商品详情列表
        goods_info:{},
        options: [{
        			icon: 'headphones',
        			text: '客服'
        		}, {
        			icon: 'shop',
        			text: '店铺',
        			info: 0,
        			infoBackgroundColor:'#007aff',
        			infoColor:"red"
        		}, {
        			icon: 'cart',
        			text: '购物车',
        			info: 0
        		}],
        	    buttonGroup: [{
        	      text: '加入购物车',
        	      backgroundColor: '#ff0000',
        	      color: '#fff'
        	    },
        	    {
        	      text: '立即购买',
        	      backgroundColor: '#ffa200',
        	      color: '#fff'
        	    }
        	    ]
      }
    },
    onLoad(options) {
     const goods_id = options.goods_id
      // 调用商品详情数据的方法
      this.getGoodsDetail(goods_id)
    },
    methods: {
       ...mapMutations('m_cart',['addToCart']),
      async getGoodsDetail(goods_id){
        const {data:res} =  await uni.$http.get('/api/public/v1/goods/detail',{ goods_id })      
        if(res.meta.status !== 200){
          return uni.$showMsg()
        }
        res.message.goods_introduce =  res.message.goods_introduce.replace(/<img/g,'<img style="display:block"').replace(/webp/g,'jpg')
         // 为data中的数据赋值
         this.goods_info = res.message
      },
       preview(i){
         uni.previewImage({
           current:i,
           urls:this.goods_info.pics.map(x => x.pics_big)
         })
       },
      onClick(e){
        console.log(e);
        if(e.content.text === '购物车'){
          uni.switchTab({
            url:'/pages/cart/cart'
          })
        }
      },
      // 右侧按钮的点击事件处理函数
      buttonClick(e) {
         // 1. 判断是否点击了 加入购物车 按钮
         if (e.content.text === '加入购物车') {
      
            // 2. 组织一个商品的信息对象
            const goods = {
               goods_id: this.goods_info.goods_id,       // 商品的Id
               goods_name: this.goods_info.goods_name,   // 商品的名称
               goods_price: this.goods_info.goods_price, // 商品的价格
               goods_count: 1,                           // 商品的数量
               goods_small_logo: this.goods_info.goods_small_logo, // 商品的图片
               goods_state: true                         // 商品的勾选状态
            }
      
            // 3. 通过 this 调用映射过来的 addToCart 方法，把商品信息对象存储到购物车中
            this.addToCart(goods)
         }
      }
    }
  }
</script>

<style lang="scss" >
  .goods-container{
    padding-bottom: 50px;
  }
  .goods_nav{
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
  }
 swiper{
  height: 750rpx;
    image{
      width: 100%;
      height: 100%;
  }
}
.goods-info-box{
  padding: 10px;
  padding-right: 0;
  .price{
    color: #C00000;
    font-size: 18px;
    margin: 10px 0;
  }
  .goods-item-body{
    display: flex;
    justify-content: space-around;
    .goods-name{
      font-size: 13px;
      margin-right: 10px;
    }
    .goods-favi{
      width: 120px;
      font-size: 12px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      border-left: 1px solid #efefef;
      color: gray;
    }
  }
  .yf{
    margin: 10px 0;
    font-size: 12px;
    color: gray;
  }
}
</style>
