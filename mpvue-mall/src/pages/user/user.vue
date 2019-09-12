<template>

  <div class="user">
   <!-- :style="{backgroundImage:'url('+userinfo.avatarUrl+')'}" -->
        <div class="user-header">
              <div class="uh-bkg">
                  <div class="userAvatar">
                      <open-data type="userAvatarUrl"></open-data>
                  </div>
                  
              </div>
              <button  open-type="getUserInfo" v-if="canIUse" :getuserinfo="bindGetUserInfo">授权登录</button>
            <h1 style="text-align:center"><open-data type="userNickName"></open-data></h1>
         </div>
         <div class="order">
           <div class="order-title">我的订单</div>
            <ul>
              <li>
                <i class="iconfont icon-pay"></i>
                <span>待付款</span>
              </li>
              <li>
                 <i class="iconfont icon-send"></i>
                 <span>待发货</span>
              </li>
              <li>
                 <i class="iconfont icon-deliver"></i>
                 <span>待收货</span>
              </li>
              <li>
                 <i class="iconfont icon-comment"></i>
                 <span>评价</span>
              </li>
              <li>
                 <i class="iconfont icon-refund"></i>
                 <span>退款/售后</span>
              </li>
            </ul>
         </div>

         <navbar pageIdx="3" />
  </div>
  


</template>

<script>

import navbar from '@/components/navbar'
export default {
   components: {navbar},
  data(){
    return{
       canIUse: wx.canIUse('button.open-type.getUserInfo'),
      userinfo:[]
    }
  },
  mounted() {
    var _this = this
    console.log(_this.userinfo)
    // wx.getSetting({
    //        success: res => {
    //          if (res.authSetting['scope.userInfo']) {
    //            // 已经授权，可以直接调用 getUserInfo 获取头像昵称，不会弹框
    //            wx.getUserInfo({
    //             success: res => {
    //                // 可以将 res 发送给后台解码出 unionId
    //               _this.userinfo= res.userInfo
    //              console.log(_this.userinfo)
    //             // 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
    //                // 所以此处加入 callback 以防止这种情况
    //               if (this.userInfoReadyCallback) {
    //                this.userInfoReadyCallback(res)
    //              }
    //             }
    //           })
    //       }
    //       }
    //      })
  },
 
  methods:{
      bindGetUserInfo(res){
        console.log(res)
      }
  },
}
</script>

<style lang="">
.user{
  position:relative;
  height:100vh;
  background: #f5f5f5
}
.user-header{
    padding-top:110px;
    height:180px;
    background:linear-gradient(to right, #66e9de,#65cfc8,#4fb8b0,#37a098);
}
.userAvatar{
  position: absolute;
  width:70px;
  height:70px;
  left:50%;
  top:30px;
  margin-left:-40px;
  border-radius:50%;
  overflow: hidden
}
.order{
  width:90%;
  margin-left: 5%;
  margin-top:5%;
  background:#fff;
  border-radius:5px;
  
}
.order ul{
  padding:10px 5px;
}

.order ul li{
  display: inline-block;
  width:20%;
  text-align:center;
  color:rgb(80, 80, 80);
}
.order ul li span{
  font-size:12px;
}
.order ul li i{
  color:#4fb8b0
}
.order-title{
  font-size:14px;
  font-weight: 600;
  border-bottom:1px solid rgb(250, 250, 250);
  padding:5px 10px;
}
h1{
  color:#fff;
}
@font-face {font-family: "iconfont";
  src: url('//at.alicdn.com/t/font_1190814_m0t5q5i3vta.eot?t=1568169710111'); /* IE9 */
  src: url('//at.alicdn.com/t/font_1190814_m0t5q5i3vta.eot?t=1568169710111#iefix') format('embedded-opentype'), /* IE6-IE8 */
  url('data:application/x-font-woff2;charset=utf-8;base64,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') format('woff2'),
  url('//at.alicdn.com/t/font_1190814_m0t5q5i3vta.woff?t=1568169710111') format('woff'),
  url('//at.alicdn.com/t/font_1190814_m0t5q5i3vta.ttf?t=1568169710111') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
  url('//at.alicdn.com/t/font_1190814_m0t5q5i3vta.svg?t=1568169710111#iconfont') format('svg'); /* iOS 4.1- */
}

.iconfont {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.icon-comment:before {
  content: "\e667";
}

.icon-deliver:before {
  content: "\e671";
}

.icon-pay:before {
  content: "\e673";
}

.icon-send:before {
  content: "\e675";
}

.icon-refund:before {
  content: "\e6ac";
}

.icon-shangpinguanli:before {
  content: "\e631";
}

.icon-meiyoudingdan-01:before {
  content: "\e618";
}

.icon-wushangpin-01:before {
  content: "\e625";
}

.icon-home:before {
  content: "\e60d";
}


</style>
