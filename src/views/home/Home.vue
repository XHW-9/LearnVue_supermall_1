<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <home-swiper :banners="banners"></home-swiper>
    <RecommendView :recommends="recommends"></RecommendView>
  </div>
</template>

<script>
  import NavBar from "@/components/common/navbar/NavBar";
  import HomeSwiper from "@/views/home/childComps/HomeSwiper";
  import RecommendView from "@/views/home/childComps/RecommendView";
  //import没有加default的，就需要加{}
  import {getHomeMultidata} from "@/network/home";


  export default {
    name: "Home",
    components:{
      RecommendView,
      NavBar,
      HomeSwiper
    },
    data(){
      return{
        banners:[],
        recommends:[],

      }
    },
    created() {
      //1.请求多个数据
      getHomeMultidata().then(res=>{
          // console.log(res);
          this.banners=res.data.banner.list;
          this.recommends=res.data.recommend.list;


        },

    )
    }
  }
</script>

<style scoped>
.home-nav{
  background-color: var(--color-tint);
  color:#fff
}

</style>
