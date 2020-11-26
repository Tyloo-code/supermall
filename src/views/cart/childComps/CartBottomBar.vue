<template>
  <div class="bottom-bar">
    <div class="check-content">
      <check-button class="check-button"/>
      <span>全选</span>
    </div>
    <div class="price">
      合计：{{totalPrice}}
    </div>
    <div class="calc">去计算({{checkLength}})</div>
  </div>
</template>

<script>
import CheckButton from 'components/content/checkButton/CheckButton.vue'
export default {
  components: { CheckButton },
  name: "CartBottomBar",
  computed: {
    totalPrice() {
      return '￥' + this.$store.state.cartList.filter(item => {
        return item.checked
      }).reduce((preValue, item) => {
        return preValue + item.price * item.count
      }, 0).toFixed(2)
    },
    checkLength() {
      return this.$store.state.cartList.filter(item => item.checked).length
    }
  }
}
</script>

<style scoped>
 .bottom-bar{
   position: relative;
   display: flex;

   height: 40px;
   line-height: 40px;
   background-color: #eee;
   font-size: 14px;
 }
 .check-content{
   display: flex;
   align-items: center;
   margin-left: 10px;
   width: 60px;
  }
 .check-button{
   width: 20px;
   height: 20px;
   line-height: 20px;
   margin-right: 5px;
 }
 .price{
   margin-left: 20px;
   flex: 1;
 }
 .calc{
   width: 90px;
   background-color: orange;
   color: #fff;
   text-align: center;
 }
</style>