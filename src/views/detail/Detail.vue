<template>
<div id="detail">
  <detail-nav-bar class="detail-nav" @titleClick="titleClick"></detail-nav-bar>
  <Scroll class="content" ref="scroll" @scroll="contentScroll">
    <datail-swiper :top-images="topImages"></datail-swiper>
    <detail-base-info :goods="goods"></detail-base-info>
    <detail-shop-info :shop="shop"></detail-shop-info>
    <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"></detail-goods-info>
    <detail-param-info :param-info="paramInfo"></detail-param-info>
  </Scroll>
  <detail-bottom-bar></detail-bottom-bar>
  <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>
</div>
</template>

<script>
import DetailNavBar from "@/views/detail/childComps/DetailNavBar";
import DatailSwiper from "@/views/detail/childComps/DatailSwiper";
import DetailBaseInfo from "@/views/detail/childComps/DetailBaseInfo";
import DetailShopInfo from "@/views/detail/childComps/DetailShopInfo";
import DetailGoodsInfo from "@/views/detail/childComps/DetailGoodsInfo";
import DetailParamInfo from "@/views/detail/childComps/DetailParamInfo";
import DetailBottomBar from "@/views/detail/childComps/DetailBottomBar";



import Scroll from "@/components/common/scroll/Scroll";
import BackTop from "@/components/content/backTop/BackTop";

import {getDetail,Goods,Shop,GoodsParam} from "@/network/detail";

export default {
  name: "Detail",
  components: {
    DetailNavBar,
    DatailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    DetailParamInfo,
    DetailGoodsInfo,
    Scroll,
    DetailBottomBar,
    BackTop
  },
  data(){
    return{
      iid:null,
      topImages:[],
      goods:{},
      shop:{},
      detailInfo:{},
      paramInfo:{},
      themeTopYs:[0,1000,2000,3000],
      isShowBackTop:false,
    }
  },
  created() {
    this.iid=this.$route.params.iid

    //2.根据iid请求详情数据
    getDetail(this.iid).then(res=>{
      //1.获取顶部的轮播数据
      const data=res.result
      this.topImages=res.result.itemInfo.topImages

      //2获取商品信息
      this.goods = new Goods(data.itemInfo,data.columns,data.shopInfo.services)

      //3,创建店铺信息的对象
      this.shop=new Shop(data.shopInfo)

      //4,保存商品详情数据
      this.detailInfo=data.detailInfo

      //5,获取参数信息
      this.paramInfo=new GoodsParam(data.itemParams.info,data.itemParams.rule)

    })

  },
  methods:{
    imageLoad() {
      this.$refs.scroll.refresh()
    },
    titleClick(index){
      this.$refs.scroll.scrollTo(0,-this.themeTopYs[index],100)
    },
    backClick(){
      this.$refs.scroll.scrollTo(0,0,500)
    },
    contentScroll(position){
      //1,判断BackTop是否显示
      this.isShowBackTop=(-position.y)>1000


    },
  }
}
</script>

<style scoped>
  #detail{
    position: relative;
    z-index: 9;
    background-color: #fff;
    height: 100vh;
  }
  .content{
    height: calc(100% - 44px - 49px);
  }

  .detail-nav{
    position: relative;
    z-index: 9;
    background-color: #fff;
  }
</style>
