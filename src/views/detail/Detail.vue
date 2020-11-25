<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav"/>
    <scroll class="content" ref="scroll">
      <detail-swiper :top-images="topImages"/>
      <detail-base-info :goods="goods"/>
      <detail-shop-info :shop="shop"/>
    </scroll>
    
  </div>
</template>

<script>

import DetailNavBar from './childComps/DetailNavBar'
import DetailSwiper from './childComps/DetailSwiper'
import {getDetail, Goods, Shop, GoodsParam} from 'network/detail'
import DetailBaseInfo from './childComps/DetailBaseInfo'
import DetailShopInfo from './childComps/DetailShopInfo'
import Scroll from 'components/common/scroll/Scroll'



export default {
  components: { DetailNavBar,DetailSwiper,DetailBaseInfo, DetailShopInfo, Scroll, },
  name: "Detail",
  data() {
    return {
      iid: null,
      topImages:[],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
    }
  },
  methods:{
    imageLoad() {
      this.$refs.scroll.refresh()
    }
  },
  created() {
    this.iid = this.$route.params.iid
    getDetail(this.iid).then(res => {
      const data = res.result
      this.topImages = data.itemInfo.topImages
      this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)
      this.shop = new Shop(data.shopInfo)
    
    })
  },

}
</script>

<style scoped>
 #detail{
    position: relative;
    z-index: 9;
    background-color: #fff;
    height: 100vh;
  }
  .detail-nav{
    position: relative;
    z-index: 9;
    background: #fff;
  }
  .content{
    height: calc(100% - 44px);
  }
</style>