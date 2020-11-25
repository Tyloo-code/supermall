<template>
  <div v-if="Object.keys(detailInfo).length !== 0" class="goods-info1">
		<div class="info-desc clear-fix">
      <div class="start">
      </div>
      <div class="desc">{{detailInfo.desc}}</div>
      <div class="end"></div>
		</div>
    <div class="info-key">{{detailInfo.detailImage[0].key}}</div>
    <div class="info-list">
      <img v-for="(item,index) in detailInfo.detailImage[0].list" :key="index" :src="item" @load="imgLoad">
    </div>
	</div>
</template>

<script>
export default {
  name: "DetailGoodsInfo",
  	props: {
			detailInfo: {
				type: Object,
				default() {
					return {}
				}
			}
    },
    data() {
      return {
        counter: 0,
        imagesLength:0
      }
    },
		methods: {
			imgLoad(){
        if (++this.counter === this.imagesLength){
          this.$emit('imgageLoad')
        }
			}
    },
    watch: {
      detailInfo() {
        this.imagesLength = this.detailInfo.detailImage[0].list.length
      }
    }
}
</script>

<style scoped>
  .goods-info1{
    padding: 20px 0;
  }
  .info-desc{
    position: relative;
  }
  .start{
    	width: 60px;
			height: 1px;
			background-color: #333;
			margin-left: 4px;
  }
  .start::before{
    position: absolute;
    left: 4px;
    top: -2.5px;
    display: block;
    content: '';
    width: 5px;
    height: 5px;
    background-color: #333;
  }
  .end{
    width: 60px;
			height: 1px;
			background-color: #333;
			position: absolute;
			right: 4px;
			bottom: 0;
  }
  .end::after{
    position: absolute;
    right: 0;
    top: -2.5px;
    display: block;
    content: '';
    width: 5px;
    height: 5px;
    background-color: #333;
  }
  .desc{
    padding: 10px 4px;
  }
  .info-key{
    font-size: 14px;
		padding-bottom: 6px;
		line-height: 20px;
		margin: 4px 0;
		text-indent: 10px;
  }
  .info-list img{
    width: 100%;
  }
</style>