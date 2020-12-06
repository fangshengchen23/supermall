<template>
  <div v-if="Object.keys(detailInfo).length !== 0 " class="goods-info">
    <div class="info-desc">
      <div class="start"></div>
      <div class="desc">{{detailInfo.desc}}</div>
      <div class="end"></div>
    </div>
    <div v-for="item in detailInfo.detailImage">
      <div class="info-key">{{item.key}}</div>
      <div class="info-list">
        <img v-for="(item1,index) in item.list" :src="item1" :key="index" @load="imgLoad" alt="">
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "DetailGoodsInfo",
    props: {
      detailInfo : {
        type: Object,
        default() {
          return {}
        }
      }
    },
    data() {
      return {
        counter: 0,
        imagesLength: 0
      }
    },
    watch: {
      detailInfo() {
        //获取图片个数
        this.imagesLength = this.detailInfo.detailImage[0].list.length
      }
    },
    methods: {
      imgLoad() {
        //判断所有图片都加载完了，再进行一次回调
        // if(++this.counter === this.imagesLength) {
          this.$emit('imageLoad');
        // }
      }
    }
  }
</script>

<style scoped>
  .goods-info {
    padding: 20px 8px;
    border-bottom: 5px solid #f2f5f8;
    background-color: #fff;
  }

  .info-desc .start, .info-desc .end{
    width: 90px;
    height: 1px;
    background-color: #a3a3a5;
    position: relative;
  }

  .info-desc .start {
    float: left;
  }

  .info-desc .end {
    float: right;
  }

  .info-desc .start::before, .info-desc .end::after {
    content: '';
    position: absolute;
    width: 5px;
    height: 5px;
    background-color: #333;
    bottom: 0;
  }

  .info-desc .end::after {
    right: 0;
  }

  .info-desc .desc {
    padding: 15px 0;
    font-size: 14px;
  }

  .info-key {
    margin: 10px 0 10px 15px;
    color: #333333;
    font-size: 15px;
  }

  .info-list img {
    width: 100%;
  }
</style>
