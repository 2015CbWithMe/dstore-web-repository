// 商品结果页面
<template>  
    <div class="filter">

       
        <scroll-view 
                @scroll="handleScroll"
                :style="{height:windowHeight+'px'}"
                scroll-y
            > 
    
           
        <!-- 商品轮播 -->
        <swiper class="swiperBox" 
            indicator-dots 
            indicator-active-color="#37a098" 
            circular vertical >
            <block v-for="(item,index) of goodsImgs" :key="index">
                <swiper-item>
                
                    <view @click="previewImg(item.path)" class="goodsImgBox" :style="{backgroundImage:'url('+item.path+')'}">

                    </view>
                </swiper-item>
            </block>
        </swiper>
         <p>{{openid}}</p>
        <!-- 商品信息 -->
        <div class="goodsInfo">
            <h1>￥{{goods.price}}</h1>
            <van-row>
                <van-col span="18">{{goods.title}}</van-col>
                <van-col span="6" class="shareBtn">
                        <button open-type="share" class="iconfont icon-fenxiang">
                            分享
                        </button>
                </van-col>
            </van-row>
            <p class="goodsBrandInfo">
                {{goods.brandinfo}}
            </p>
        </div>

       
        <!-- 商品图文介绍 -->
        <div class="goodsImgInfo">
            <image v-for="(item,index) of imgDetail " :key="index" :src="item" mode ="aspectFill" lazy-load="true">
            </image>
        </div>
        
    </scroll-view>
    <!-- 用户操作按钮 -->
    <div class="paybar">
            <div class="blurBar" v-if="!hiddenBlurbar"></div>
            <button class="chat iconfont icon-xiaoxi1" open-type="contact"></button>
          
            <div class="gopay" @click="buyaction">购买</div>
            <div class="addcart iconfont icon-gouwuche" @click="userAction"></div>
    </div>
   
    <!-- 操作上拉菜单 -->
    <van-action-sheet
            :show="show"
            :close-on-click-overlay="true"
            @close="onClose"
            @select="onselect"
            >
       <div class="actionBox" > 
            <div class="actionTop">
                <div class="atopBox">
                    <div class="topImg">
                        <img :src="actionImg" mode="aspectFill">
                    </div>
                    <div class="topInfo">
                        <p>{{goods.title}}</p>
                        <span>￥{{goods.price}}</span>
                    </div>
                </div>
                 
            </div>
            <scroll-view scroll-y class="actionBottom" @scroll="handlescroll">
                <div class="selectArea" v-for="(item,index) of sku" :key="index">
                    <h1 class="selectType">{{item.name}}</h1>
                    <div class="selectItemBox">
                        <div class="selectItem"  :class="{'active':childitem.isactive}" 
                            @click="skuselect(childIdx,index,childitem.skuimg)" 
                            v-for="(childitem,childIdx) of item.skuarr" :key="childIdx" >
                            <image v-if="item.name =='选择颜色'" :src="childitem.skuimg" mode="aspectFill"></image>
                           {{childitem.type}}
                        </div>
                    </div>
                 </div>
                 
                 <van-cell-group>
                    <van-cell center custom-class="qtyBox" title="购买数量" :border="false">    
                         <van-stepper @change="handleQty" :value="1" integer input-width="40px"/>
                    </van-cell>
                </van-cell-group>
                 
            </scroll-view>
             <div class="acbtn">
                <button v-if="confirmtype == 1" @click="addtocart">确定</button>
                <button v-if="confirmtype == 2" @click="buygoods(goods.id,goods.brand)">确定</button>
             </div>
       </div>
      
            
        
    </van-action-sheet>
     <van-toast id="van-toast" />
</div>
</template>

<script>
export default {
    data(){
        return{
            page:1,
            goods:[],
            windowHeight:0,
            goodsImgs:[],
            previewImgArr:[],
            
            sku:[
                {
                    name:'选择颜色',
                    skuarr:[
                        {
                           type: ' 006黑色/白色logo'
                        },{
                            type:' 005蓝色/白色logo'
                        }
                      ],
                },
                {
                    name:'尺码',
                    skuarr:[
                        {type:'165/84A/S'},
                        {type:'170/88A/M'},
                        {type:'175/92A/L'},
                        {type:'180/96A/XL'},
                        {type:'185/100A/XXL'}
                        ],
                }
            ],
            show: false,
            imgDetail:[
                'https://www.dongyiself.xyz/detail/d1.jpg',
                'https://www.dongyiself.xyz/detail/d2.jpg',
                'https://www.dongyiself.xyz/detail/d3.jpg',
                'https://www.dongyiself.xyz/detail/d4.jpg'
            ],
            hiddenBlurbar:false,
            currentIndex:1,
            actionImg:'',
            cindex:-1,
            pindex:-1,
            startY:0,
            moveY:0,
            cartdata:'',
            qty:0,
            openid:'',
            buynum:1,
            confirmtype:0
        }
    },
 
    beforeMount(){
            this.goods = []
            this.sku.map(item=>{
                item.skuarr.map(skuitem=>{
                    this.$set(skuitem,'isactive',false)
                })
            })
      //console.log(this.sku)
         var _this = this
       
         wx.getSystemInfo({ //设置商品图片宽度
            success (res) {
                _this.windowHeight = res.windowHeight-55 ;
                _this.getGoodsList()
             }
         })
        wx.getStorage({//获取存储的openid
            key: 'openid',
            success (res) {
              _this.openid = res.data
            }
        })
        
    },
    //分享事件
    onShareAppMessage(res) {
            if (res.from !== 'button') return false;
            return {
              title: this.goods.title,
              imageUrl: this.goods.cover,
            };
        },
    methods:{
        //规格上拉菜单
        userAction(){
            this.show = !this.show
            this.actionImg = this.goodsImgs[0].path
            this.confirmtype = 1
             this.sku.map(item=>{
                item.skuarr.map(skuitem=>{
                    this.$set(skuitem,'isactive',false)
                })
            })
        },
        buyaction(){
            this.show = !this.show
            this.actionImg = this.goodsImgs[0].path
            this.confirmtype = 2
             this.sku.map(item=>{
                item.skuarr.map(skuitem=>{
                    this.$set(skuitem,'isactive',false)
                })
            })
        },

        //购买数量
        handleQty(e){
           this.buynum = e.mp.detail
        },
    //选中规格样式切换
        skuselect(cidx,pidx,simg){
           
            this.$set(this.sku[pidx].skuarr[cidx],'isactive',true)
            this.actionImg = simg
            this.sku[pidx].skuarr.map((item,index)=>{
                if(index != cidx){
                        this.$set(item,'isactive',false)
                    }
            })

        },
        //购买
        buygoods(gid,brand){
            wx.showToast({
                title: '个人账户不能使用支付接口，所以购买后续功能未开发',
                icon: 'none',
                duration: 2000
                })
        },
        //加入购物车
        addtocart(){
             this.cartdata= ''
             //获取选中规格值
             this.sku.map(item=>{
            
                item.skuarr.map(arritem=>{
                    
                    if(arritem.isactive){
                        this.cartdata+=arritem.type+';'
                    }
                    
                })
            })
           
            if(this.cartdata){
                //加入购物车接口
                this.$httpWX.post({
                        url:'/addcart',
                        data:{
                                id:this.openid,
                                goodsid:this.$root.$mp.query.id,
                                buynum:this.buynum,
                                spec :this.cartdata
                            },
                    
                })
                .then(result=>{
                   
                     this.show =false
                     wx.showToast({
                        title: '已添加到购物车',
                        icon: 'success',
                        duration: 2000
                        })
                })
            }  
            
        },
        
   
     //上拉菜单关闭事件
     onClose(){
            this.show = false
     },
    
     
     //点击轮播图，放大预览图片
    previewImg(src){
            var _this = this
           
         wx.previewImage({
            current: src, // 当前显示图片的http链接
            urls: _this.previewImgArr // 需要预览的图片http链接列表
        })
    },
    //商品详情
    getGoodsList(){
            var _this = this
            this.$httpWX.get({
                 url:'/goodsDetail',
                 data:{
                     id:_this.$root.$mp.query.id
                }
            })
            .then(result=>{
                console.log(result)
                    //预览图片组
                    _this.previewImgArr = []
                    _this.goods = result[0]
                    //轮播图组
                    _this.goodsImgs = result[0].goodsimg
                        
                    _this.goodsImgs.map((item,index)=>{
                        //商品规格图组
                        _this.$set(_this.sku[0].skuarr[index],'skuimg',item.path)
                       _this.previewImgArr.push(item.path)
                    })
                    //设置商品名称为顶部标题
                    wx.setNavigationBarTitle({
                        title:result[0].title.length > 20 ? result[0].title.slice(0,20)+ '...' : result[0].title
                    })
            })
        }
    }
}
</script>
<style lang="">
 
    .swiperBox{
        
        height:375px
    }
   
    .goodsImgBox{
        width:100%;
        height:100%;
        background-size:cover;
        background-position: center center;
    }
    .goodsInfo{
        padding:10px;
    }
    .goodsInfo h1{
        padding:10px 0;
        font-weight: 600;
    }
  
    .shareBtn button{
       position:absolute;
        right:-4px;
        font-size:10px !important;
        line-height:20px !important;
        border-top-left-radius: 20px;
        border-bottom-left-radius: 20px;
        background:rgb(236, 236, 236);
        color:rgb(126, 126, 126);
    }
    .shareBtn button::after{
        border:none;
        
    }
    .goodsSku{
        border-top:6px solid rgb(245, 245, 245);
        padding:10px;
        font-size:10px;
    }
    .al{
        float: right;
        font-size:14px;
        color:#ccc;
        vertical-align: sub;
   }
   
   .paybar{
       z-index:99;
       position:fixed;
       bottom:0;
       width:100%;
       display:flex;
       padding:10px 0;
       justify-content: space-evenly;
       align-items: center
   }
   .blurBar{
       position:absolute;
       top:-30px;
       width:100%;
       height:100%;
       background:#fff;
       filter:blur(20px)
   }
   .gopay{
       z-index:99;
       width:30%;
       height:35px;
       text-align:center;
       background:linear-gradient(to right, #47c5ba,#49aba4,#4fb8b0,#37a098);;
       border-radius: 20px;
       color:#fff;
       line-height:35px;
       font-size:12px;
       box-shadow: 0px 6px 10px 0 #37a098;
   }
   .chat,.addcart{
        z-index:99;
        width:35px;
       height:35px;
       border-radius:50%;
       background:transparent;
       text-align:center;
       line-height: 35px;
       font-size:22px;
        color:rgb(211, 210, 210);
   }
   .chat{
       padding:0;
       margin:0;
      line-height:36px;

   }
   .chat::after{
       border:0;
   }
   .goodsImgInfo image{
       width:100%;
   }
   .sectionTitle{
       
       color:#999;
       padding:10px 0;
       text-align:center;
       background-color:rgb(245, 245, 245)
   }
   .sectionTitle span{
    position:relative;
    font-size:12px;
   }
   
   .sectionTitle span::before,.sectionTitle span::after{
       content:'';
       position:absolute;
       top:9px;
       width:50px;
       height:1px;
       background:rgb(236, 236, 236)
   }
  .sectionTitle span::before{
      left:-60px
  }
  .sectionTitle span::after{
      right:-60px
  }
  .goodsBrandInfo{
      padding:.5rem 0;
      font-size:12px;
      color:rgb(126, 125, 125);
  }
.goodsImgInfo{
    padding:0 16px
}
.sheet-index--van-action-sheet{
    min-height:70%;
    border-top-left-radius: 20px;
    border-top-right-radius: 20px;
}

.actionTop{
    position: fixed;
    top:0;
    left:0;
    width:100%;
    background:#fff;
    padding:0 10px;
}
.atopBox{
    display:flex;
    justify-content: space-around;
    border-bottom:1px solid #eee;
}
.topImg{
    padding:10px ;
}
.topImg img{
    width:100px;
    height:100px;
}
.topInfo{
    width:40%;
    padding:10px 0;
}
.topInfo p{
    font-size:12px;

}
.topInfo span{
    display:block;
    font-size:16px;
    font-weight: 600;
    margin-top:5px;
}
.actionBottom{
    
    height:350px;
    padding:120px 10px 0 10px;
}
.selectType{
    font-size:12px;
    padding:10px 0;
}
.selectItemBox{
    display:flex;
    flex-wrap: wrap;
    justify-content: flex-start
}
.selectItem{
    background:#f1f1f1;
    font-size:10px;
    padding:4px;
    border-radius:4px;
    margin-right:10px;
    margin-bottom:10px;
}
.selectItem image{
    width: 20px;
    height: 20px;
    border-radius: 4px;
    vertical-align: middle;

}
.size .selectItem{
    padding:6px;
}
.qtyBox{
    padding:10px  0 !important;
}
.active{
    color:#37a098;
    border:1px solid #37a098
}
.acbtn{
    z-index:999;
    position:fixed;
    bottom:0;
    width:100%;
    padding:.2rem 0;
    background:#fff;
}
.acbtn button{
   
    width:90%;
    height:30px;
    margin-left:5%;
    line-height:30px;
    font-size:16px;
    border-radius:20px;
    background: linear-gradient(to right, #47c5ba,#49aba4,#4fb8b0,#37a098);
    color:#fff;
    
}
</style>
