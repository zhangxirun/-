<template>
  <view>
    <view class="address-box" v-if="JSON.stringify(address) === '{}'">
      <button type="primary" size="mini" class="BtnAddAdress" @click="chooseAddress">请选择收货地址+</button>
    </view>
    <view class="address-info-box" v-else @click="chooseAddress">
      <view class="row1">
        <view class="row-left">
          <view class="username">收货人：{{address.userName}}</view>
        </view>
       <view class="row-right">
         <view class="address-phone">电话：{{address.telNumber}}</view>
         <uni-icons type="arrowright" size="16"></uni-icons>
       </view>
      </view>
      <view class="row2">
        <view class="row2-left">
          收货地址:
        </view>
        <view class="row2-right">
          {{addstr}}
        </view>
      </view>
    </view>
    <!-- 底部边框线 -->
    <image src="../../static/cart_border@2x.png"  class="address-border"></image>
  </view>
</template>

<script>
  import {mapState,mapMutations,mapGetters} from 'vuex'
  export default {
    name:"my-address",
    computed:{
      ...mapState('m_user',['address']),
      ...mapGetters('m_user',['addstr'])
    },
    data() {
      return {
        // address:{},
      };
    },
    methods:{
      ...mapMutations('m_user',['updateAddress']),
     async chooseAddress(){
       wx.chooseAddress({
           success: (result)=>{
             console.log(result);
             wx.setStorageSync("address", result);
           },
           fail: ()=>{},
           complete: ()=>{}
         })
       const [err,succ] = await uni.chooseAddress().catch(err => err)
       console.log(succ);
       console.log(err);
       if(err === null && succ.errMsg === 'chooseAddress:ok'){
         // this.address = succ
         this.updateAddress(succ)
       }
       if(err && (err.errMsg === 'chooseAddress:fail auth deny' || err.errMsg === 'chooseAddress:fail authorize no response')){
         console.log('需要重新授权');
         this.reAuth()
       }
     },
     async reAuth(){
       const [err2,confirmResult] = await uni.showModal({
         content:'检测到您没打开权限，是否去设置打开？',
         confirmText:'确认',
         confirmText:'取消'
       })
       if(err2) return
       
       if(confirmResult.cancel) return uni.$showMsg('您取消了授权')
       if(confirmResult.confirm) return uni.openSetting({
         success:(settingResult) => {
           console.log(settingResult);
           if(!settingResult.authSetting['scope.address']) return uni.$showMsg('您取消了授权')
           if(settingResult.authSetting['scope.address']) return uni.$showMsg('授权成功！请选择收货地址')
         }
       })
     }
    }
  }
</script>

<style lang="scss">
.address-border{
  display: block;
  width: 100%;
  height: 5px;
}
.address-box{
  display: flex;
  justify-content: center;
  align-items: center;
  height: 90px;
}
.address-info-box{
  display: flex;
  height: 90px;
  font-size: 12px;
  padding: 5px;
  flex-direction: column;
  justify-content: center;
  .row1{
    display: flex;
    justify-content: space-between;
    .row-right{
      display: flex;
      justify-content: space-between;
    }
    }
    .row2{
      display: flex;
      align-items: center;
      margin-top: 10px;
      .row2-left{
        white-space: nowrap;
      }
      .row2-right{}
    }
}
</style>