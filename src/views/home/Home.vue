<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <tab-control :class="{fixed:isTabFixed} "
                 :titles="['流行','新款','精选']"
                 @tabClick="tabClick"
                 ref="tabControl"></tab-control>
    <scroll class="content"
            ref="scroll"
            :probe-type="3"
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="loadMore">
      <home-swiper :banners="banners"
                   @swiperImageLoad="swiperImageLoad"></home-swiper>
      <RecommendView :recommends="recommends"></RecommendView>
      <feature-view></feature-view>
      <tab-control :class="{fixed:isTabFixed} "
                   :titles="['流行','新款','精选']"
                   @tabClick="tabClick"
                    ref="tabControl"></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>

  </div>
</template>

<script>
  import HomeSwiper from "@/views/home/childComps/HomeSwiper";
  import RecommendView from "@/views/home/childComps/RecommendView";
  import FeatureView from "@/views/home/childComps/FeatureView";

  import NavBar from "@/components/common/navbar/NavBar";
  import TabControl from "@/components/content/tabControl/TabControl";
  import GoodsList from "@/components/content/goods/GoodsList";
  import Scroll from "@/components/common/scroll/Scroll";
  import BackTop from "@/components/content/backTop/BackTop";


  //import没有加default的，就需要加{}
  import {
    getHomeMultidata,
    getHomeGoods
  } from "@/network/home";


  export default {
    name: "Home",
    components:{
      GoodsList,
      RecommendView,
      NavBar,
      HomeSwiper,
      FeatureView,
      TabControl,
      Scroll,
      BackTop,
    },
    data(){
      return{
        banners:[],
        recommends:[],
        goods:{
          'pop':{page:0,list:[]},
          'new':{page:0,list:[]},
          'sell':{page:0,list:[]},
        },
        currentType:'pop',
        isShowBackTop:false,
        tabOffsetTop:0,
        isTabFixed:false

      }
    },
    computed:{
      showGoods(){
        return this.goods[this.currentType].list
      }
    },
    created() {
      //1.请求多个数据
      this.getHomeMultidata()
        //2.请求商品数据
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')




    },
    mounted() {
      //1.监听Item中图片加载完成
      const refresh = this.debounce(this.$refs.scroll.refresh,200)


      this.$bus.$on('itemImageLoad',()=>{
        // this.$refs.scroll && this.$refs.scroll.refresh()
        refresh()
      })

    },
    methods:{
      //事件监听相关发发
      swiperImageLoad(){
        this.tabOffsetTop=this.tabOffsetTop = this.$refs.tabControl.$el.offsetTop
      },

      debounce(func,delay){
        let timer=null

        return function (...args){
          if(timer)clearTimeout(timer)
          timer=setTimeout(()=>{
            func.apply(this,args)
          },delay)
        }
      },

      tabClick(index){
        switch (index){
          case 0:
            this.currentType='pop'
            break
          case 1:
            this.currentType='new'
            break
          case 2:
            this.currentType='sell'
            break
        }
      },

      backClick(){
        this.$refs.scroll.scrollTo(0,0,500)
      },
      contentScroll(position){
        //1,判断BackTop是否显示
        this.isShowBackTop=(-position.y)>1000

        //2.决定tabControl是否吸顶position:fixed
        this.isTabFixed=(-position.y)>this.tabOffsetTop


      },
      loadMore(){
        this.getHomeGoods(this.currentType)
        // this.$refs.scroll.scroll.refresh()
      },

      //网络请求相关方法
      getHomeMultidata(){
        getHomeMultidata().then(res=>{
          // console.log(res);
          this.banners=res.data.banner.list;
          this.recommends=res.data.recommend.list;
        })
      },
      getHomeGoods(type){
        const page = this.goods[type].page+1
        getHomeGoods(type,page).then(res=>{
          this.goods[type].list.push(...res.data.list)
          this.goods[type].page+=1
          this.$refs.scroll.finishPullUp()
        })
      }
    }
  }
</script>

<style scoped>
#home{
  padding-top: 44px;
  height: 100vh ;
  position: relative;
}
.home-nav{
  background-color: var(--color-tint);
  color:#fff;
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9;
}

.tab-control{
  /*position:sticky;*/
  top:44px;
  z-index: 9;
}

.content{
  /*height: 300px;*/
  overflow:hidden;

  position: absolute;
  top:44px;
  bottom: 49px;
  left: 0;
  right: 0;
}

.fixed{
  position: fixed;
  left: 0;
  right: 0;
  top:44px;
}

/*.content{*/
/*  height: calc(100% - 98px);*/
/*  !*overflow:hidden;*!*/
/*  margin-top: 44px;*/
/*}*/
</style>
