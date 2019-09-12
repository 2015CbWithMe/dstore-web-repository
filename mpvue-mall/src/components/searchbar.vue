<template>
   
    <div class='searchBar'>
        <!-- <view @click="searchFuc"><van-icon class="sicon" name="search" ></van-icon></view>  -->
        <button @click="searchFuc" class="sicon"><van-icon name="search" ></van-icon></button>
        <form action="javascript:return true">
             <input confirm-type="search"  @confirm="searchFuc" value="" v-model="searchKey" :placeholder="placeholder">
        </form>
       
        <van-toast id="van-toast" />
    </div>
    
</template>
<script>
import Toast from '../../dist/wx/static/vant/toast/toast';
export default {
    props:['isfilterpage','placeholder'],
   
    data(){
        return{
            searchKey:''
        }
    },
    methods: {
        searchFuc(){
             wx.setNavigationBarTitle({
                title:'搜索'
            })
           if(this.searchKey == ''){
               Toast(
                   {
                       message:'请输入搜索关键词',
                       position:'top'
                   })
               return
           }
            if(!this.isfilterpage){
               wx.navigateTo({
                         url:"../filter/main?brandid=&gid=&skey=" + this.searchKey
                    })
                
            }else{
                this.$emit('skey',this.searchKey)
            }
             this.searchKey = ''
             
        }
    },
}
</script>
<style lang="">
     .searchBar{
        position: fixed;
        width:100%;
        display: flex;
        justify-content: center;
        padding:15px 0 ;
        background-color:#fff;
        z-index:99
    }
    .searchBar input{
        position:relative;
        z-index:99;
        width:65%;
        height:25px;
        background-color:#f7f6fb;
        border-radius:20px;
        padding-left: .5rem;
        font-size:12px;
        padding-right:40px;
    }
    .sicon{
        z-index:999;
        position:absolute;
        right:40px;
        height:30px;
        line-height:30px;
        background:transparent;
        
        color:#d6d5da;
        cursor: pointer;
    }
    .sicon::after{
        border:0 !important;
    }
   
</style>