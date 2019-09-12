// 商品结果页面
<template>
<scroll-view class="filter" 
            @scrolltolower="loadMoregoods"
            :style="{height:windowHeight+'px'}"
            scroll-y='true' 
> 
    <!-- 搜索组件 -->
     <searchBar @skey="getSearchKey"  :isfilterpage='true' :placeholder="searchkey" />
         <div class=" pt40">
            
            <!-- 商品过滤栏 -->
            <div class="filterBar">
                <div @click="changeActive(0)" :class="{'active':activeIdx == 0}">热门</div>
                <div @click="changeActive(1)" :class="{'active':activeIdx == 1}">销量</div>
                <div @click="changeActive(2)" :class="{'active':activeIdx == 2}" class="price">
                    价格
                    <van-icon :class="[activeIdx == 2 ? (isUpsign ? 'active' : 'noactive') : '']" class="up" name="arrow-up"></van-icon> 
                    <van-icon :class="[activeIdx == 2 ? (isUpsign ? 'noactive' : 'active') : '']" class="down" name="arrow-down"></van-icon>
                </div>
            </div>
           
            <view class="goods-box">

                <view class="goods-item" v-for="(item,index) of filterGoodsList" :key="index" >
                            <view  
                                @click="goToDetail(item.id)"
                                style="position:relative;z-index:1" >
                                
                                    <image class="goods_img" :src="item.cover" mode="aspectFill" lazy-load></image>
                                
                            </view>
                            <view class="goods_lable">
                                <view class="titleBox">
                                        <text class="goods-title black"> {{item.title}}</text>
                                </view>
                               <view class="goods-title  gprice black">$ {{item.price}} <span>{{item.sales}}人付款</span> </view>
                           </view>
                    </view>
                    
                    
            </view>
            <view class="loadFinish" v-if="goodsList.length == totalPage">
                已加载完全部数据
            </view>
        </div>
    </scroll-view>
</template>

<script>
import searchBar from '@/components/searchBar'
export default {
    components:{searchBar},
    data(){
        return{
            activeIdx:0,
            isUpsign:false,
            page:1,
            goodsList:[],
            totalPage:0,
            windowHeight:0,
            searchkey:'',
            brandid:'',
        }
    },
    computed: {
        filterGoodsList(){
            if(this.activeIdx == 1){
                return this.goodsList.sort((a,b)=>b.sales - a.sales)
            }else if(this.activeIdx == 2){
                if(this.isUpsign){
                    return this.goodsList.sort((a,b)=>b.price - a.price)
                } else{
                    return this.goodsList.sort((a,b)=>a.price - b.price)
                }
                }else{
                    
                return this.goodsList.sort((a,b)=>b.hot  - a.hot)
            }
            
        }
    },
    beforeMount(){
        //初始化页数
         this.page = 1
         this.goodsList=[]
         //搜索值填充placeholder
         if(this.$root.$mp.query.skey){
            this.searchkey = this.$root.$mp.query.skey
         }else{
             this.searchkey=''
         }

         this.brandid = this.$root.$mp.query.brandid
      
         var _this = this
        
         wx.getSystemInfo({ //设置商品图片宽度
            success (res) {
                _this.windowHeight = res.windowHeight;
                _this.getGoodsList()
             }
         })
         wx.setNavigationBarTitle({
            title:this.$root.$mp.query.brand
        })
    },
    methods:{
        changeActive(idx){
            this.activeIdx = idx
            this.isUpsign = !this.isUpsign
        },
            goToDetail(id){
                wx.navigateTo({
                    url:'../goodsdetail/main?id='+id
                })
            },
        //获取搜索值进行搜索
        getSearchKey(k){
             this.searchkey = k
             this.brandid = ''
             this.page = 1
             this.goodsList=[]
             this.getGoodsList()
        },
        loadMoregoods(){
            //不直接调用loadgoods是为了防止用户多次滚动到底部请求
            if(this.loadFinshed){
                return
            }
            this.getGoodsList()
        },
        getGoodsList(){
            var _this = this
            this.$httpWX.get({
                 url:'/goods',
                 data:{
                  page:_this.page++,
                  pageSize:10,
                  brandid:_this.brandid,
                  gid:_this.$root.$mp.query.gid,
                  skey:_this.searchkey
                }
            })
            .then(result=>{
                 _this.totalPage = result.totalPage
              
                result.goods.map(item=>{
                    _this.goodsList.push(item)
                })
                  console.log( _this.goodsList)
                //判断已加载完所有数据
                if(this.goodsList.length == this.totalPage){
                    this.loadFinshed = true
                }
            })
        }
    }
}
</script>
<style lang="" scoped>
    .filterBar{
        z-index:99;
        position:fixed;
        width:100%;
        display: flex;
        background:#fff;
        padding:10px 0;
        border-bottom:1px solid rgb(247, 247, 247)
    }
    .filterBar div{
        width:33.33%;
        text-align:center;
        font-size:12px;
        font-weight: 600
    }
    .active{
        color:#37a098;
    }
    .noactive{
        color:#000 !important;
    }
    .price{
        position:relative
    }
    .up,.down{
        position:absolute !important;
        font-size:12px;
        transform: scale(.8)
    }
    .up{
        top:-1px;
    }
    .down{
        top:5px;
    }
    .filterGoodsBox{
        margin-top:.6rem;
    }
  .goods-box{
        display: flex;
        flex-wrap:wrap;
        margin-top:.6rem;
        justify-content: space-evenly
    }
   .goods-box::after{
        content:'';
        width:45%;
        padding:10px 0;
    }
    .goods-item{
        width:45%;
        padding:10px 0;
    }
    .goods_img{
        width:100%;
        height:180px;
        background-size:cover;
        background-position: center center;
        background-repeat: no-repeat
    }
    .titleBox{
        display:flex;
        flex-wrap: wrap;
        align-items: center;
        height:30px;
    }
    .goods-title{
        overflow: hidden;
        text-overflow: ellipsis;
        display: -webkit-box;
        word-wrap: break-word;
        word-break:break-all;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        font-size:10px;
        color:rgb(80, 80, 80);
        margin-top:5px;
    }
   
    .gprice{
        font-size:12px;
        font-weight: 600;
        color:#000;
        padding:5px 0;
    }
    .gprice span{
        float:right;
        color:rgb(80, 80, 80);
        font-weight: normal;
        font-size:10px;
    }
    .loadFinish{
        width:100%;
        text-align:center;
        font-size:14px;
        padding:10px 0;
        color:rgb(160, 160, 160);
    }
</style>