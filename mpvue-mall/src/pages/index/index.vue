<template>
  <div>
    <scroll-view  class="sv" scroll-y='true' 
                 :style="{height:windowHeight+'px'}"
                 @scrolltolower="loadMoregoods"
    >
      
      <swiper class="swiper01" autoplay indicator-dots interval="5000" duration="500">
          <block v-for="(item,index) of imgUrls" :key="index">
              <swiper-item>
              <view  :style="{backgroundImage:'url('+item.src+')'}" class="slide-image">
                  <text class="imgTitle">{{item.title}}</text>
              </view>
              </swiper-item>
          </block>
      </swiper>
       <view class="tag">HOT SALES</view>
       <view class="goods-box">
     
            <view class="goods-item" v-for="(item,index) of goodsList" :key="index"  @click="goToDetail(item.id)">
                  
                          
                            <image class="goods_img" :src="item.cover" mode="aspectFill" lazy-load></image>
       
                    <view class="goods_label">
                        <text class="goods-title black">$ {{item.price}} </text>
                        <text class="goods-title black"> {{item.title}}</text>
                    </view>
              </view>
            <view class="loadFinish" v-if="goodsList.length == totalPage">
                已加载完全部数据
            </view>
            
      </view>
    </scroll-view> 
     <navbar pageIdx="0" />
  </div>
     
</template>

<script>
//自定义tabbar
import navbar from '@/components/navbar'

export default {
   components: {
    navbar
  },
  data () {
    return {
      windowHeight:0,
      goodsList:[],
      totalPage:0,
      loadFinshed:false,
      page:1,
      imgUrls:[
          {
            'src':'https://www.dongyiself.xyz/image/20190813141514.jpg',
            'title':'CLOT携手Nike退出全新服装系列,架起篮球文化与中国传统文化之间的桥梁'
          },
          {
            'src':'https://www.dongyiself.xyz/image/20190813141525.jpg',
            'title':'Off-White x Nike WMNS Vapor Street系列预计十月发售'

          }
        ],
    }
  },

 
 beforeMount () {
   var _this = this
    wx.getSystemInfo({ //设置商品图片宽度
        success (res) {
          _this.windowHeight = res.windowHeight;
          _this.loadgoods()
        }
      })
    
  },
  mounted() {
    
  },
  methods: {
      goToDetail(id){
            wx.navigateTo({
                url:'../goodsdetail/main?id='+id
            })
        },
    loadMoregoods(){
      //不直接调用loadgoods是为了防止用户多次滚动到底部请求
      if(this.loadFinshed){
          return
      }
      this.loadgoods()
    },
      loadgoods(){
       
        var _this = this
        this.$httpWX.get({
              url:'/goodsList', 
                data:{
                  page:_this.page++,
                  pageSize:10
                }
          }).then(result=>{
              _this.totalPage = result.totalPage
              
              result.goods.map(item=>{
                _this.goodsList.push(item)
              })
              //判断已加载完所有数据
              if(this.goodsList.length == this.totalPage){
                this.loadFinshed = true
              }
              
            
          })
          
      }
  },

 
}
</script>

<style>
html,body,view,text {
box-sizing:border-box;

}

@font-face {
  font-family: 'Poiret One';
  font-style: normal;
  font-weight: 400;
  src: local('Poiret One'), local('PoiretOne-Regular'), url(https://fonts.gstatic.font.im/s/poiretone/v7/UqyVK80NJXN4zfRgbdfbo55cVw.woff2) format('woff2');
  unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+02C6, U+02DA, U+02DC, U+2000-206F, U+2074, U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215, U+FEFF, U+FFFD;
}
.header{display:flex;justify-content: space-between;padding:1rem}

.storeTitle{
   font-size:16px
}
/* .sv{
  padding-bottom:50px
} */
.leftMenu,.search{
  font-size:18px
}
.loadFinish{
  padding:1rem 0;
}
/* .container{
  transform: translateZ(100px)
} */
/**轮播***/
.search{
  color:#fff;
  background:#ccc;
  width:80%;
  border-radius: 3px;
}
.swiper01{
  height:170px;
}
.slide-image{
  position: relative;
  width:100%;
  height:170px;
  background-size:cover;
  background-position: center;
}
.imgTitle{
  position: absolute;
  bottom:30px;
  width:100%;
  padding:5px 10px;
  background-color:rgba(0, 0, 0, 0.42);
  font-size:14px;
  color:#fff;
}
.tag{
  position:relative;
  font-size:14px;
  padding:10px 10px;
  color:#2a2a2a;
  font-family: 'Poiret One', sans-serif;
}

.hotGoods{
  display: flex;
  text-align:center;
  border-radius:10px;
  
}
.hg_img{
  width:100%;
  height:100%;
  background-image:url('https://www.dongyiself.xyz/image/imgUploader_1556419800834_ba.png');
  background-size:cover
}
.hotGoods>view{
  width:33.33%;
  padding:0 10px;
 }
.hgBkg{
  position: relative;
  background-size:cover;
  background-position: center;
}

.hg>view{
  height:100%;
}
.hgleft>view{
  height:100px;
  
}
.hgleftTextBOX{
  display: flex;
  justify-content: center;
  align-content: flex-end;  
  flex-flow: row wrap;
  position: absolute;
  top:0;
  left:0;
  width:100%;
  height:100%;
  background-color:rgba(0, 0, 0, 0.15);
  padding:5px;
  color:#fff;
}
.hg-text{
  position:absolute;
  bottom:15px;
  font-size:10px;
  overflow:hidden;
  max-height:30px;
  
}
.hg-price{
  font-size:14px;
}
.goods-box{
  display: flex;
  flex-wrap:wrap;
  justify-content: space-evenly
}
.goods-item{
  width:45%;
  padding:10px 0;
}
.goods_img{
  width:100%;
  height:180px;
  background-size:cover;
  background-position: center;
}
.goods_label{
  margin-top:5px
}
.goods-title{
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  max-height:50px;
  font-size:12px;
  line-height:19px;
}


.test-scroll view{
  font-size:4em;
  line-height: 8em
}
.test-scroll{
  height:568px;
}
button{
  position:relative;
  z-index:99;
}
.loadFinish{
  font-size:14px;
}
</style>
