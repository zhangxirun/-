<template>
  <view class="goods-item">
    <!-- 左侧区域 -->
    <view class="goods-item-left">
      <radio :checked="goods.goods_state" color="#C00000" v-if="showRadio" @click="radioClickHandler"></radio>
      <image :src="goods.goods_small_logo || defaultPic" class="goods-pic"></image>
    </view>
    <!-- 右侧区域 -->
    <view class="goods-item-right">
      <!-- 商品的名字 -->
      <view class="goods-name">{{goods.goods_name}}</view>
      <view class="goods-info-box">
        <view class="goods-price">￥{{goods.goods_price | toFixed }}</view>
        <uni-number-box :min="1" :value="goods.goods_count" v-if="showNum" @change="numchangeHadler"></uni-number-box>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-goods",
    props:{
      goods:{
        type:Object,
        default:{}
      },
      showRadio:{
        type:Boolean,
        default:false
      },
      showNum:{
        type:Boolean,
        default:false
      }
    },
    data() {
      return {
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      };
    },
    filters:{
      toFixed(num){
      return  Number(num).toFixed(2)
      }
    },
    methods:{
      radioClickHandler(){
        this.$emit('radio-change',{
          // 商品ID
          goods_id: this.goods.goods_id,
                // 商品最新的勾选状态
          goods_state:!this.goods.goods_state
        })
      },
      numchangeHadler(val){
        this.$emit('num-change',{
          // 商品ID
          goods_id: this.goods.goods_id,
          // 商品最新的勾选状态
          goods_count:+val
        })
      }
    }
  }
</script>

<style lang="scss">
.goods-item{
  width: 750rpx;
  box-sizing: border-box;
  display: flex;
  padding: 10px 5px;
  border-bottom: 1px solid #f0f0f0;
  .goods-item-left{
    margin-right: 5px;
    display: flex;
    justify-content: space-around;
    align-items: center;
    .goods-pic{
      width: 100px;
      height: 100px;
      display: block;
    } 
  }
  .goods-item-right{
    display: flex;
    flex-direction: column;
    flex: 1;
    justify-content: space-between;
    .goods-name{
      font-size: 13px;
    }
    .goods-info-box{
      display: flex;
      justify-content: space-between;
      align-items: center;
      .goods-price{
        color: #C00000;
        font-size: 16px;
      }
    }
  }
}
</style>