<template>

  <div class="cart">
   
      <div class="cartContent">
        <div class="cartheader">
          <div class="cartTitle">
            购物车
            <span class="Goodscount">共{{countgoods}}件宝贝</span>
          </div>
          <div class="manage" @click="handleManage">管理</div>
        </div>
          <div v-if="goods.length == 0" class="cartEmpty">
            <i class="iconfont icon-wushangpin-01"></i>
              购物车啥也没有~
          </div>
          <div class="cgitem" v-for="(item,index) of goods" :key="index">
            <div class="cgheader clearfix">
               <div class="radiobox ">
                 <van-checkbox checked-color="#37a098" 
                                icon-class="checkicon" 
                                :value="item.checked" 
                                @change="handleMainCbox(index)"
                                class="mainCheck"></van-checkbox>
                </div>
               <div class="shopname">{{item.brand}} <van-icon name="arrow" /> </div>
            </div>
            
            <div class="cgbody clearfix" v-for="(bitem,bidx) of item.goodsinfo" :key="bidx" >
                <van-swipe-cell right-width="85" class="bitemBox clearfix" >
                  <div @click="goToDetail(bitem.goodsid)">
                      <div class="radiobox">
                          <van-checkbox icon-class="checkicon" 
                            class="otherCheck" checked-color="#37a098" 
                            @change="handleSingleCbox(index,bidx)"
                            v-model="bitem.price"
                          :value="bitem.checked"></van-checkbox>
                      </div>
                      <div class="cgimg"><image :src="bitem.cover" mode="aspectFill" /></div>
                      <div class="cginfo">
                          <p class="cgtitle">{{bitem.title}}</p>
                          <p class="cgspec">{{bitem.spec}} {{bitem.goodsid}}</p>
                          <van-cell :border="false" :title="'￥'+bitem.price" custom-class="cg-cell" title-class="cgprice" >
                            <van-stepper :value="bitem.quantity"
                              integer
                              min="1"
                              input-width="40px"
                              input-class="stepinp" 
                              plus-class="stepbtn"
                              minus-class="stepbtn"
                            
                              @plus="handleplus(index,bidx)"
                              @minus="handleminus(index,bidx)"
                            />
                          </van-cell>
                      </div>
                      
                  </div>
                   <view slot="right">
                        <view class="swipe-del" @click="delFromCart(2,bitem.goodsid)">删除</view>
                      </view>
                </van-swipe-cell>
            </div>
          </div>
          </div>
      
      <div class="countMoney">
              
              <div class="cmleft">
                  <van-checkbox icon-class="checkicon" 
                      class=" checkall" checked-color="#37a098" 
                      @change="handleAllCbox" name="全选"
                    :value="checkAll" >全选</van-checkbox>
              </div>
              <div class="cmright countbox">
                  <div class="normal" v-if="!ismanage">
                    <div>
                      合计: <span class="countprice">￥{{countPrice}}</span> 
                    </div>
                    <div class="jiesuan">
                        <div class="jiesuanBtn">结算({{jscount}})</div>
                    </div>
                  </div>
                  <div class="manageSet" v-else>
                      <div class="del" @click="delFromCart(1)">删除</div>
                  </div>
                   
              </div>
                    
      </div>
      <navbar pageIdx="2" />
  </div>
  


</template>

<script>

import navbar from '@/components/navbar'
export default {
   components: {navbar},
  data(){
    return{
       windowHeight:0,
      goods:[],
      countgoods:0,
      checkAll:false,
      singlechecked:false,
      totalprice:0,
      checkarr:[],
      ismanage:false,
      goodsidstr:"",
      openid:''
    }
  },
  beforeMount() {
    
   
    var _this = this
     wx.getSystemInfo({ //设置商品图片宽度
            success (res) {
                _this.windowHeight = res.windowHeight-54;
             }
        })
    wx.getStorage({
      key: 'openid',
      success (res) {
         _this.openid = res.data
         _this.getCartgoods()
      }
    })
  },
 
  onShow(){
     this.getCartgoods()
  },
  computed: {
    //计算总价
      countPrice(){
           this.totalprice = 0
           this.goods.map(item=>{
                  item.goodsinfo.map(bitem=>{
                      if(bitem.checked){
                         this.totalprice+=bitem.price * bitem.quantity
                      }
                  })
                })
                return this.totalprice
      },
      jscount(){
          var count = 0
          this.goodsidstr=''
          this.goods.map(item=>{
                  item.goodsinfo.map(bitem=>{
                      if(bitem.checked){
                         count++
                      }
                  })
                })
                return count
      }
  },
  methods:{
     goToDetail(id){
       console.log(id)
             wx.navigateTo({
                url:'../goodsdetail/main?id='+id
            })
        },
    delFromCart(deltype,gid){
     
      if(deltype == 1){
         
          this.goods.map(item=>{
          item.goodsinfo.map(item=>{
            if(item.checked){
               this.goodsidstr += item.goodsid+','
            }
             
          })
        })
         this.goodsidstr = this.goodsidstr.substr(0,this.goodsidstr.length-1)
      }else{
        this.goodsidstr = gid
      }
      
         this.$httpWX.post({
            url:'/delgoodsfromcart',
            data:{
               goodsid:this.goodsidstr,
               openid:this.openid
            }
           
        })
        .then(result=>{
           if(result.code == 1){
              this.getCartgoods() 
           }
        })
        
    },
    //监听管理点击状态
     handleManage(){
        this.ismanage = !this.ismanage
     },
     //增加商品数量
     handleplus(idx,bidx){
        this.goods[idx].goodsinfo[bidx].quantity++
     },
     //减少商品数量
      handleminus(idx,bidx){
        this.goods[idx].goodsinfo[bidx].quantity--
     },
     
    //店铺商品全选
    handleMainCbox(idx){
     if(!this.goods[idx].checked) {
          this.$set(this.goods[idx],'checked',true)
          this.goods[idx].goodsinfo.map(item=>{
                this.$set(item,'checked',true)
          })
      }else{
          this.$set(this.goods[idx],'checked',false)
          this.goods[idx].goodsinfo.map(item=>{
                this.$set(item,'checked',false)
          })
      }
     },
     //单个商品选择
     handleSingleCbox(idx,bidx){
        var countcheck = 0
        if(this.goods[idx].goodsinfo[bidx].checked){
           
            this.$set(this.goods[idx].goodsinfo[bidx],'checked',false)
        }else{
            
            this.$set(this.goods[idx].goodsinfo[bidx],'checked',true)
        }
        //将购物车单个店铺内商品一个一个全部勾选完时，店铺商品全选框被选中
        this.goods[idx].goodsinfo.map(item=>{
          //循环勾选中的goodsinfo数组
          //checked=true的countcheck加1
            if(item.checked){
                countcheck++
                //通过此数组的长度判断是否全部被选中
                if(countcheck == this.goods[idx].goodsinfo.length){
                    this.$set(this.goods[idx],'checked',true)
                    
                }
            }
            if(countcheck != this.goods[idx].goodsinfo.length){
                    this.$set(this.goods[idx],'checked',false)
              }
            
            
        })
        // this.countPrice()
     },
     //全选所有购物车商品
     handleAllCbox(){
       this.checkAll = !this.checkAll
       if(this.checkAll){
            this.goods.map(item=>{
            this.$set(item,'checked',true)
            item.goodsinfo.map(bitem=>{
              this.$set(bitem,'checked',true)
            })
          })
       }else{
          this.goods.map(item=>{
            this.$set(item,'checked',false)
            item.goodsinfo.map(bitem=>{
              this.$set(bitem,'checked',false)
            })
          })
       }
     },
     
      getCartgoods(){
        var _this =this
        this.$httpWX.get({
          url:'/cartgoods',
          data:{
            openid:_this.openid
          }
        })
        .then(result=>{
          console.log(result)
           this.countgoods = 0
            this.goods= result
            this.goods.map(item=>{
              this.$set(item,'checked',false)
              item.goodsinfo.map(gitem=>{
                  this.$set(gitem,'checked',false)
                   this.countgoods = this.countgoods+1
              })
            })
          
        })
      }
      
  },
}
</script>

<style>
.cartEmpty i{
  font-size:4em;
  color:#eee;
}
.cartEmpty{
  padding:1rem 0;
  text-align:center;
  color:#ddd
}
.cmleft,.cmright,.normal>div{
  width:50%;
  float: left
}
.normal{
  display: flex;
  align-items: center ;
  line-height: 40px;
}
.cmleft{
  padding-top:10px
}

.jiesuanBtn,.del{
    width: 100%;
    height: 30px;
    text-align: center;
    background: linear-gradient(to right, #47c5ba,#49aba4,#4fb8b0,#37a098);
    border-radius: 40rpx;
    color: #fff;
    line-height: 30px;
    font-size: 24rpx;

}
.manageSet{
  padding-top:7.5px;
}
.del{
  float:right;
   width:40%;
   height: 25px;
   line-height:25px;
   background:transparent;
   border:1px solid #f7547b;
   color:#f7547b;
   
}
.countbox{
  font-weight: 600;
}
.countprice{
  color:#37a098;
  font-weight: normal
}
 .countMoney{
   box-sizing:border-box;
   z-index:999;
   position:fixed;
   bottom:48px;
   left:0;
   width:100%;
   height:40px;
   border-top:1px solid #eee;
   background:#fff;
   font-size:12px;
   padding:0 10px;
 }
 .countMoney .van-checkbox__icon{
   position:relative;
   top:3px;
 }
  /* .cart{
    height:100vh;
    background:#fcfbfc;
    padding:0 15px;
  } */
  .cartContent{
    padding:0 15px 80px 15px;
  }
  .cgheader{
    padding:8px 0;
  }
  .cgitem{
    background:#fff;
    margin-bottom:10px;
    border-radius:8px;
    box-shadow: 0px 5px 11px 0px #f6f6f6;
    padding:5px ;
  }
  .mainCheck{
    padding-top:2px;
  }
  .otherCheck{
    padding-top:23px;
  }
  .van-checkbox__icon{
    width:15px !important;
    height:15px !important;
  }
  .checkicon{
    line-height: 1 !important;
  }
  .cgprice{
    color:#37a098;
    font-size:14px;
  }
  .cg-cell{
    padding:10px 0 !important;
  }
  .shopname{
    font-size:14px;
    font-weight: 600
  }
  .shopname van-icon{
    vertical-align: middle;
    color:#e7e7e7
  }
  .radiobox{
    width:10%;
    float: left;
    display:flex;
    align-items: center;
  }
  .stepinp{
    box-sizing:border-box !important;
    min-height: 20px;
    height:20px !important;
    border-top:1px solid #f5f5f5 !important;
    border-bottom:1px solid #f5f5f5 !important;
    padding:0 !important;
    margin:0 !important;
    background:#fff !important;
    font-size:12px !important;
  }
  .stepbtn{
    z-index:99;
    width:20px !important;
    height:20px !important;
    padding:0 !important;
    margin:0 !important;
    background:#fff !important;
     
  }
  .stepbtn{
    border:1px solid #f5f5f5 !important;
  }
  
  .clearfix{
    overflow: hidden;
  }
  .cgimg{
    width:20%;
    float: left;
  }
  .cgimg image{
    width:60px;
    height:60px;
  }
  .cginfo{
    width:70%;
    float: left;
  }
  .cgtitle{
    overflow: hidden;
    max-height: 32px;
    font-size:12px;
    margin-bottom:3px;
  }
  .swipe-del{
    max-height: 107px;
    background: #ee0a24;
    min-height: 89px;
    line-height: 5.5;
    width: 80px;
    text-align: center;
    color: #fff;

  }
  .cgspec{
    display: inline-block;
    background:#f8f8f8;
    font-size:10px;
    padding:3px ;
    color:#959595
  }
 .shopname{
   width:90%;
   float: left; 
  }
 .cartheader{
   display: flex;
   justify-content: space-between;
   padding:.5rem 0;
 }
 .cartTitle{
   width:20%;
   font-weight: 600;
 }
 .manage{
   width:10%;
   font-size:12px;
   line-height: 25px;
 }
 .Goodscount{
   display: block;
   margin-top:10px;
   font-size:10px;
   font-weight: normal
 }
 @font-face {font-family: "iconfont";
  src: url('//at.alicdn.com/t/font_878693_f78eoyigj1.eot?t=1568102001771'); /* IE9 */
  src: url('//at.alicdn.com/t/font_878693_f78eoyigj1.eot?t=1568102001771#iefix') format('embedded-opentype'), /* IE6-IE8 */
  url('data:application/x-font-woff2;charset=utf-8;base64,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') format('woff2'),
  url('//at.alicdn.com/t/font_878693_f78eoyigj1.woff?t=1568102001771') format('woff'),
  url('//at.alicdn.com/t/font_878693_f78eoyigj1.ttf?t=1568102001771') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
  url('//at.alicdn.com/t/font_878693_f78eoyigj1.svg?t=1568102001771#iconfont') format('svg'); /* iOS 4.1- */
}

.iconfont {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.icon-search:before {
  content: "\e65c";
}

.icon-discover:before {
  content: "\e67e";
}

.icon-cart:before {
  content: "\e6af";
}

.icon-home:before {
  content: "\e6b8";
}

.icon-people:before {
  content: "\e736";
}

.icon-caidan05:before {
  content: "\e604";
}

.icon-user3:before {
  content: "\e8b1";
}

.icon-gouwuche2:before {
  content: "\e708";
}

.icon-wode2:before {
  content: "\e61e";
}

.icon-yuan:before {
  content: "\e629";
}

.icon-wushangpin-01:before {
  content: "\e625";
}

.icon-home1:before {
  content: "\e644";
}

.icon-menu:before {
  content: "\e8c6";
}

</style>
