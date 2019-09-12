<script>
export default {
  created () {
    // 调用API从本地缓存中获取数据
    /*
     * 平台 api 差异的处理方式:  api 方法统一挂载到 mpvue 名称空间, 平台判断通过 mpvuePlatform 特征字符串
     * 微信：mpvue === wx, mpvuePlatform === 'wx'
     * 头条：mpvue === tt, mpvuePlatform === 'tt'
     * 百度：mpvue === swan, mpvuePlatform === 'swan'
     * 支付宝(蚂蚁)：mpvue === my, mpvuePlatform === 'my'
     */
   
    wx.hideTabBar() //隐藏原生tabbar
    wx.login({
    success (res) {
        wx.request({  
            //获取openid接口  
          url: 'https://www.dongyiself.xyz/login',  
          data:{
            js_code:res.code,   
          },  
          method:'post',  
          success:function(res){  
            
              wx.setStorage({
                  key:"openid",
                  data:res.data
              })
              //console.log('openid:'+res.data)
          }  ,
          fail:function(err){
            console.log(err)
          }
        })
    }
  })

      let logs
      if (mpvuePlatform === 'my') {
          logs = mpvue.getStorageSync({key: 'logs'}).data || []
          logs.unshift(Date.now())
          mpvue.setStorageSync({
            key: 'logs',
            data: logs
          })
      } else {
          logs = mpvue.getStorageSync('logs') || []
          logs.unshift(Date.now())
          mpvue.setStorageSync('logs', logs)
      }
  },
  // log () {
  //   console.log(`log at:${Date.now()}`)
  // }
}
</script>

<style>

@font-face {font-family: "iconfont";
  src: url('//at.alicdn.com/t/font_1241121_mc98hv3tdb.eot?t=1566549700908'); /* IE9 */
  src: url('//at.alicdn.com/t/font_1241121_mc98hv3tdb.eot?t=1566549700908#iefix') format('embedded-opentype'), /* IE6-IE8 */
  url('data:application/x-font-woff2;charset=utf-8;base64,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') format('woff2'),
  url('//at.alicdn.com/t/font_1241121_mc98hv3tdb.woff?t=1566549700908') format('woff'),
  url('//at.alicdn.com/t/font_1241121_mc98hv3tdb.ttf?t=1566549700908') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
  url('//at.alicdn.com/t/font_1241121_mc98hv3tdb.svg?t=1566549700908#iconfont') format('svg'); /* iOS 4.1- */
}

.iconfont {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
view,scroll-view{
  box-sizing: border-box;
}
.icon-kuliankulian:before {
  content: "\e6c0";
}

.icon-changyonglogo30:before {
  content: "\e717";
}

.icon-iconfontzhizuobiaozhun22:before {
  content: "\e615";
}

.icon-dongjie:before {
  content: "\e646";
}

.icon-gouwuche:before {
  content: "\e64f";
}

.icon-shouji:before {
  content: "\e616";
}

.icon-weixin1:before {
  content: "\e659";
}

.icon-fenxiang:before {
  content: "\e624";
}

.icon-setting-user:before {
  content: "\e609";
}

.icon-xiaoxi:before {
  content: "\e6eb";
}

.icon-xiaoxi1:before {
  content: "\e633";
}

.icon-xiangshang1:before {
  content: "\e638";
}

.icon-xiangshang:before {
  content: "\e606";
}


.container {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 200rpx 0;
  box-sizing: border-box;
}
.pt40{
        padding-top:40px;
    }
/* this rule will be remove */
* {
  transition: width 2s;
  -moz-transition: width 2s;
  -webkit-transition: width 2s;
  -o-transition: width 2s;
  
  

}

</style>
