<template>
  <view class="my-search-container" :style="{'background':bgcolor}" @click="searchGoto">
    <view class="my-search-box" :style="{'border-radius':radius + 'px'}">
      <uni-icons type="search" size="17"></uni-icons>
      <text class="placeholder">搜索</text>
    </view>
  </view>
</template>

<script>
  export default {
    props:{
      // 背景颜色
      bgcolor:{
        type:String,
        default: '#C00000'
      },
      // 边框圆角
      radius:{
        type:Number,
        default:18 
      }
    },
    name:"my-search",
    data() {
      return {
        
      };
    },
    methods:{
      searchGoto(){
        this.$emit('click')
        console.log('222');
      }
    }
  }
</script>

<style lang="scss">
.my-search-container {
  // background-color: #c00000;
  height: 50px;
  padding: 0 10px;
  display: flex;
  align-items: center;
}

.my-search-box {
  height: 36px;
  background-color: #ffffff;
  // border-radius: 15px;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;

  .placeholder {
    font-size: 15px;
    margin-left: 5px;
  }
}
</style>