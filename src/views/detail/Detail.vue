<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" ref="nav"/>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      <detail-swiper :top-images="topImages"/>
      <detail-base-info :goods="goods"/>
      <detail-shop-info :shop="shop"/>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"/>
      <detail-param-info ref="params" :param-info="paramInfo"/>
      <detail-comment-info ref="comment" :comment-info="commentInfo"/>
      <goods-list ref="recommend" :goods="recommends"/>
    </scroll>
    <detail-bottom-bar @addToCart="addToCart"/>
    <back-top @click.native="backClick" v-show="isShowBackTop" />
  </div>
</template>

<script>

import DetailNavBar from './childComps/DetailNavBar'
import DetailSwiper from './childComps/DetailSwiper'
import {getDetail, Goods, Shop, GoodsParam ,getRecommend} from 'network/detail'
import DetailBaseInfo from './childComps/DetailBaseInfo'
import DetailShopInfo from './childComps/DetailShopInfo'
import Scroll from 'components/common/scroll/Scroll'
import DetailGoodsInfo from './childComps/DetailGoodsInfo.vue'
import DetailParamInfo from './childComps/DetailParamInfo.vue'
import DetailCommentInfo from './childComps/DetailCommentInfo.vue'
import GoodsList from 'components/content/goods/GoodsList.vue'
import {debounce} from 'common/utils'
import {itemListenerMixin} from 'common/mixin'
import DetailBottomBar from './childComps/DetailBottomBar.vue'
import BackTop from 'components/content/backTop/BackTop.vue'




export default {
  components: { DetailNavBar,DetailSwiper,DetailBaseInfo, DetailShopInfo, Scroll,
                DetailGoodsInfo, DetailParamInfo, DetailCommentInfo, GoodsList, 
                DetailBottomBar, BackTop,  },
  name: "Detail",
  mixins: [itemListenerMixin],
  data() {
    return {
      iid: null,
      topImages:[],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      themeTopYs: [],
      getThemeTopY: null,
      itemImgListener: null,
      currentIndex: 0,
      isShowBackTop: false,
      
    }
  },
  methods:{
    imageLoad() {
      this.$refs.scroll.refresh()
    },
    titleClick(index) {
       this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 200)
    },
     backClick() {
      this.$refs.scroll.scrollTo(0, 0);
    },
    contentScroll(position) {
      const positionY = -position.y
      let length = this.themeTopYs.length
      for(let i = 0; i < length; i++) {
        if (this.currentIndex !== i && (i < length - 1 && positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1]) 
        || (i === length - 1 && positionY >= this.themeTopYs[i])) {
          this.currentIndex = i
          this.$refs.nav.currentIndex = this.currentIndex
        }
      }
      this.isShowBackTop = (-position.y) > 1000
    },
    addToCart() {
      const product = {}
      product.image = this.topImages[0]
      product.title = this.goods.title
      product.desc = this.goods.desc
      product.price = this.goods.realPrice
      product.iid = this.iid

      // this.$store.commit('addCart',product)
      this.$store.dispatch('addCart',product)
    }
  },
  mounted() {
    
  },
  updated() {
    this.getThemeTopY = debounce ( () => {
        this.themeTopYs = []
        this.themeTopYs.push(0)
        this.themeTopYs.push(this.$refs.params.$el.offsetTop)
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
        this.themeTopYs.push(this.$refs.recommend.$el.offsetTop)
    })
    
      this.getThemeTopY()
    
      
  },
  destroyed() {
    this.$bus.$off('itemImageLoad', this.itemImgListener)
  },
  created() {
    this.iid = this.$route.params.iid
    getDetail(this.iid).then(res => {
      const data = res.result
      this.topImages = data.itemInfo.topImages
      this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)
      this.shop = new Shop(data.shopInfo)
      this.detailInfo = data.detailInfo;
      this.paramInfo = new GoodsParam(data.itemParams.info,data.itemParams.rule)

      if(data.rate.cRate !== 0){
        this.commentInfo = data.rate.list[0]
      }   

    })
    getRecommend().then(res => {
      this.recommends = res.data.list
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
    height: calc(100% - 44px - 49px);
    overflow: hidden;
    width: 100%;
  }
</style>