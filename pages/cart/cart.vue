<template>
  <view class="cart-container" v-if="cart.length !== 0">
    <my-address></my-address>
    <view class="shop-cart">
      <!-- 图标 -->
      <uni-icons type="shop" size="30"></uni-icons>
      <!-- 右侧文本 -->
      <text class="cart-title">购物车</text>
    </view> 
    <uni-swipe-action>
      <block v-for="(goods,i) in cart" :key="i">
        <uni-swipe-action-item :right-options="options"  @click="onClick(goods)">
    <my-goods :goods="goods" :showRadio="true" @radio-change="radioChangeHandler" :showNum="true" @num-change="numchangeHadler"></my-goods>
        	</uni-swipe-action-item>
      </block>
    </uni-swipe-action>
    <my-settle></my-settle>
  </view>
  <view class="empty-cart" v-else>
    <image src="@/static/cart_empty@2x.png"  class="empty-img"></image>
    <text class="tip-text">空空如也</text>
  </view>
</template>

<script>
  import {mapState,mapMutations} from 'vuex'
  import badgeMix from '@/mixins/tabbar-badge.js'

  export default {
    mixins:[badgeMix],
    computed:{
      ...mapState('m_cart',['cart']),
    },
    data() {
      return {
        options:[
          {
          text:'删除',
          style:{
            backgroundColor:'#C00000'
          }
        },
        ]
      }
    },
    methods:{
     ...mapMutations('m_cart', ['updateGoodsState','updateGoodsCount','removeGoodsId']),
         // 商品的勾选状态发生了变化
         radioChangeHandler(e) {
           this.updateGoodsState(e)
         },
      numchangeHadler(e){
        this.updateGoodsCount(e)
      },
      onClick(goods){
        this.removeGoodsId(goods.goods_id)
      }
    }
  }
</script>

<style lang="scss">
  .cart-container{
    padding-bottom: 50px;
  }
  .shop-cart{
    display: flex;
    align-items: center;
    padding-left: 5px;
    border-bottom: 1px solid #efefef;
    height: 40px;
    .cart-title{
      margin-left: 10px;
    }
  }
  .empty-cart{
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-top: 150px;
    .empty-img{
      width: 90px;
      height: 90px;
    }
    .tip-text{
      font-size: 12px;
      color: gray;
      margin-top: 15px;
    }
  }
</style>
