<template>
    <div class="discover">
       <searchBar :isfilterpage="false"/>
        <div class="pt40">
            <div class="tagLeft">
                <div 
                    v-for="(item,index) of classifyName" 
                    :class="[index == activeIdx ? 'activeTag' : '']" 
                    :key="index"
                    @click="changeActive(index)"
                 >
                    {{item.name}}
                 </div>
            </div>
            <div  class="tagRight">
                <div class="rcontent" v-show="activeIdx == 0">
                    <div class="rtitle">
                        <span>品牌</span>
                     </div>
                     
                     <div
                        class="listItem"
                         
                         v-for="(item,index) of classIfyList" 
                         @click="gofilter(item.brandid,'',item.text)"
                         :key="index" 
                         v-if="item.logo"
                         :style="{backgroundImage:'url('+item.logo+')'}"
                         :class="item.customClass"
                        >
                    </div>
                     
                </div>
                <div v-show="activeIdx == 1">
                        <div  
                            class="childList"
                            v-for="(item,index) of clothesList" 
                             @click="gofilter(item.brandid,sortid,item.brand)"
                            :key="index" >
                              <div class="rtitle">
                                <span>{{item.brand}}</span>
                              </div>
                            
                                <div 
                                    v-for="(ichild,cindex) of item.goods" :key="cindex"
                                    class="childImg"
                                    v-if="cindex < 3"
                                    :style="{backgroundImage:'url('+ichild.cover+')'}">
                                </div>
                         </div>
                </div>
                 <div v-show="activeIdx == 2">
                        <div  
                            class="childList"
                            v-for="(item,index) of shoesList" 
                            @click="gofilter(item.brandid,sortid,item.brand)"
                            :key="index" >
                                <div class="rtitle">
                                <span>{{item.brand}}</span>
                                </div>
                            
                                <div 
                                    v-for="(ichild,cindex) of item.goods" :key="cindex"
                                    class="childImg"
                                    v-if="cindex < 3"
                                    :style="{backgroundImage:'url('+ichild.cover+')'}">
                                </div>
                         </div>
                 </div>
            </div>
           
        </div>
        <navbar pageIdx="1" />
    </div>
</template>
<script>
import navbar from '@/components/navbar'
import searchBar from '@/components/searchBar'
export default {
    components: {navbar,searchBar},
    data(){
        return{
            //分类左标题
            classifyName:[
                    {'name':'品牌','sorttype':'brand','sortid':'brand_id'},
                    {'name':'潮搭','sorttype':'gtype','sortid':0},
                    {'name':'鞋类','sorttype':'gtype','sortid':1}
                ],
            //分类列表
            classIfyList:[],
            clothesList:[],
            shoesList:[],
            //根据不同sorttype查询不同分类列表
            sorttype:'brand',
            sortid:'brand_id',
            activeIdx:0,
            bidarr:[]
        }
    },
    beforeMount(){
        this.getclassIfyList()
        wx.setNavigationBarTitle({
            title:'分类'
        })
    },
    methods: {
        
        //左边点击状态切换及更新分类列表
        changeActive(idx){
            this.activeIdx = idx
            this.sorttype = this.classifyName[idx].sorttype
            this.sortid = this.classifyName[idx].sortid
            this. getclassIfyList()
        },
        gofilter(id,b,c){
            
            wx.navigateTo({
                url:'../filter/main?brand='+c+'&brandid='+id+'&gid='+b
            })
        },
        getclassIfyList(){
            var _this = this
             this.$httpWX.get({
                 url:'/category',
                 data:{
                     sorttype:_this.sorttype,
                     sortid:_this.sortid
                 }
             })
             .then(result=>{
                     var cl =[]
                  
                    result.map(item=>{
                        if(_this.activeIdx == 0){
                            
                            cl.push(
                                {
                                    text:item.brand,
                                    brandid:item.brand_id,
                                    logo:'https://www.dongyiself.xyz/logo/'+item.brand+'.png',
                                    customClass:item.brand.toLowerCase()
                                }
                            )
                            _this.classIfyList = cl
                            
                       }else if(_this.activeIdx == 1){

                             _this.clothesList = result
                             console.log(result)

                        }else if(this.activeIdx == 2){

                              _this.shoesList = result

                        }
                    })
                    
                    
             })
        }
    },
}
</script>
<style>
  
    .tagLeft{
        position:fixed;
         height:100vh;
        overflow:auto;
        width:25%;
        left:0;
    }
    .tagRight{
        position: absolute;;
        height:100%;
        overflow:auto;
        left:25%;
        width:75%;
      }
    .rcontent{
        display: flex;
        display: -webkit-flex;
        justify-content: space-between;
        flex-direction: row;
        flex-wrap: wrap;
    }
    .listItem{
        width:13%;
        height:0;
        padding-bottom:15%;
        margin:3% 8%;
        background-size:100%;
        background-position: center center;
        background-repeat: no-repeat;
    }
    .rtitle{
        width:100%;
        display: flex;
        justify-content: center;
       text-align:center;
        font-size:12px;
        font-weight: 600;
        padding:.3rem 0;
    }
    .rtitle span{
         position:relative;
     }
    .rtitle span::before{
        content:'';
        position:absolute;
        width:25px;
        height:1px;
        left:-40px;
        top:8px;
        background:#f7f6fb
    }
    .rtitle span::after{
        content:'';
        position:absolute;
        width:25px;
        height:1px;
        right:-40px;
        top:8px;
        background:#f7f6fb
    }
    .childList{
        display:flex;
        justify-content: space-between;
        flex-direction: row;
        flex-wrap: wrap;
    }
    .childImg{
        width:18%;
        height:0;
        padding-bottom:18%;
        background-size:cover;
        background-position:center center;
         margin:2% 5%;
    }
    .childList::after{
        content:'';
        width:18%;
        margin:2% 5%;
    }
    .bape{
        background-size:70% !important;
    }
    .tagLeft{
        height:100vh;
        background:#f7f6fb;
    }
    .tagLeft div{
        text-align:center;
        line-height:50px;
        font-size:12px;
    }
    .activeTag{
        background:#fff;
        color:#37a098;
        font-weight:600
    }
</style>