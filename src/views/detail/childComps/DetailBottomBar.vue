<template>
  <div class="bottom-bar">
    <div class="bar-item bar-left">
      <div>
        <img src="~assets/img/detail/service.svg" alt="">
        <span class="text">客服</span>
      </div>
      <div>
        <img src="~assets/img/detail/shop.svg" alt="">
        <span class="text">店铺</span>
      </div>
      <div>
        <img src="~assets/img/detail/collect.svg" alt="">
        <span class="text">收藏</span>
      </div>
    </div>
    <div class="bar-item bar-right">
      <div class="cart" @click="addToCart">加入购物车</div>
      <div class="buy">购买</div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "DetailBottomBar",
    methods: {
      addToCart() {
        this.$emit('addToCart')
      }
    }
  }
</script>

<style scoped>
  .bottom-bar {
    height: 49px;
    background-color: #ffffff;

    position: relative;
    left: 0;
    right: 0;
    /*bottom: 49px;*/

    display: flex;
    text-align: center;
  }

  .bar-item {
    flex: 1;
    display: flex;
  }

  .bar-item>div {
    flex: 1;
  }

  .bar-left img {
    display: block;
    width: 22px;
    height: 22px;
    margin: 6px auto 1px;
  }

  .bar-left .text {
    font-size: 13px;
    font-size: 13px;
  }

  .bar-right {
    line-height: 49px;
    font-size: 15px;
  }

  .bar-right .cart{
    background-color: #ffe817;
    color: #333;
  }

  .bar-right .buy{
    background-color: #f69;
    color: #ffffff;
  }
</style>
