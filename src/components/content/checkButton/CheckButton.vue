<template>
  <div class="check-button" :class="{check: isChecked}">
    <img src="~assets/img/cart/tick.svg" alt="">
  </div>
</template>

<script>
  export default {
    name: "CheckButton",
    props: {
      isChecked: {
        type: Boolean,
        default: false
      }
    }
  }
</script>

<style scoped>
  .check-button {
    width: 16px;
    height: 16px;
    border-radius: 50%;
    border: 2px solid #aaa;
    overflow: hidden;
  }

  .check-button img {
    margin: -2px 0 0 -2px;
  }

  .check {
    border-color: red;
    background-color: red;
  }
</style>
