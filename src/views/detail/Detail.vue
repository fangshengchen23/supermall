<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" ref="nav"/>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      <detail-swiper :top-images="topImages"/>
      <detail-base-info :goods="goods"/>
      <detail-shop-info :shop="shop"/>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad" />
      <detail-param-info ref="params" :param-info="paramInfo"/>
      <detail-comment-info ref="comments" :comment-info="commentInfo"/>
      <goods-list ref="recommends" :goods="recomments"/>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"/>
    <detail-bottom-bar @addToCart="addToCart"/>
  </div>
</template>

<script>
  import DetailNavBar from "./childComps/DetailNavBar";
  import DetailSwiper from "./childComps/DetailSwiper";
  import DetailBaseInfo from "./childComps/DetailBaseInfo";
  import DetailShopInfo from "./childComps/DetailShopInfo";
  import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
  import DetailParamInfo from "./childComps/DetailParamInfo";
  import DetailCommentInfo from "./childComps/DetailCommentInfo";
  import DetailBottomBar from "./childComps/DetailBottomBar"

  import Scroll from "components/common/scroll/Scroll"
  import GoodsList from "components/content/goods/GoodsList";

  import {getDetail, Goods, Shop, GoodsParam, getRecommend} from "network/detail";

  import {debounce} from "common/utils";
  import {itemListenerMixin,backTopMixin} from "common/mixin"

  import { mapActions } from "vuex"
  export default {
    name: "Detail",
    components: {
      DetailNavBar,
      DetailSwiper,
      DetailBaseInfo,
      DetailShopInfo,
      DetailGoodsInfo,
      DetailParamInfo,
      DetailCommentInfo,
      DetailBottomBar,
      Scroll,
      GoodsList
    },
    data() {
      return {
        iid: null,
        topImages: [],
        goods: {},
        shop: {},
        detailInfo: {},
        paramInfo: {},
        commentInfo: {},
        recomments: [],
        themeTopYs: [],
        getThemeTopY: null,
        positionY: 0,
        currentIndex: 0,
      }
    },
    mixins: [itemListenerMixin,backTopMixin],
    methods: {
      ...mapActions(['addCart']),
      imageLoad() {
        this.refresh()

        this.getThemeTopY()
      },
      titleClick(index) {
        // console.log(index)
        this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 200)
      },
      contentScroll(position) {
        // 1.1 获取y值
        const positionY = -position.y
        // console.log(positionY)

        // 1.2 positionY和主题中值进行对比
        // [0, 24882, 25694, 25979]

        // positionY 在0和24882间，index = 0
        // positionY 在24882和25694间，index = 1
        // positionY 在25694和25979间，index = 2

        // positionY 超过25979时，index = 3
        let length = this.themeTopYs.length
        for (let i = 0; i < length-1; i++) {
          // if (this.currentIndex !== i && ((i < length - 1 && positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1]) ||
          //   (i == length -1 && positionY >= this.themeTopYs[i]))) {
          //   this.currentIndex = i;
          //   // console.log(this.currentIndex)
          //   this.$refs.nav.currentIndex = this.currentIndex
          // }

          if (this.currentIndex !== i && (positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1])) {
            this.currentIndex = i;
            // console.log(this.currentIndex)
            this.$refs.nav.currentIndex = this.currentIndex
          }
        }

        // 2.判断BackTop是否显示
        this.isShowBackTop = Math.abs(position.y) > 1000
      },
      addToCart() {
        // 1.获取购物车需要展示的信息
        const product = {}
        product.image = this.topImages[0];
        product.title = this.goods.title;
        product.desc = this.goods.desc;
        product.price = this.goods.realPrice;
        product.iid = this.iid

        // 2.将商品添加到购物车里
        // this.$store.cartList.push(product)
        this.addCart(product).then(res => {
          // this.show = true;
          // this.message = res;
          //
          // setTimeout(() => {
          //   this.show = false
          // },2000)
          // console.log(this.$toast)
          this.$toast.show(res,2000)
        })

        // this.$store.dispatch('addCart', product).then(res => {
        //   console.log(res)
        // })
      }
    },
    created() {
      // 1.保存传入的iid
      this.iid = this.$route.params.iid

      // 2.根据iid请求详情数据
      getDetail(this.iid).then(res => {
        console.log(res)
        // 1.获取顶部的图片轮播图数据
        const data = res.result;
        this.topImages = data.itemInfo.topImages;

        // 2.获取商品信息
        this.goods = new Goods(data.itemInfo,data.columns,data.shopInfo.services)

        // 3.获取店铺信息
        this.shop = new Shop(data.shopInfo)

        // 4.获取商品详细数据
        this.detailInfo = data.detailInfo;

        // 5.获取商品的参数信息
        this.paramInfo = new GoodsParam(data.itemParams.info,data.itemParams.rule)

        // 6.获取商品的用户评论信息
        if (data.rate.cRate !== 0) {
          this.commentInfo = data.rate.list[0]
        }

        // this.$nextTick(() => {
        //   // 根据最新的数据，对应的DOM是已经被渲染出来
        //   // 但是图片依然是没有加载完（目前获取到offsetTop不包含其中的图片）
        //   // offsetTop值不对的时候，都是因为图片的问题
        //   this.themeTopYs.push(0);
        //   this.themeTopYs.push(this.$refs.params.$el.offsetTop);
        //   this.themeTopYs.push(this.$refs.comments.$el.offsetTop);
        //   this.themeTopYs.push(this.$refs.recommends.$el.offsetTop);
        //   console.log(this.themeTopYs)
        // })
      })

      // 3.请求推荐数据
      getRecommend().then(res => {
        this.recomments = res.data.list
      })

      // 4.给getThemeTopYs赋值
      this.getThemeTopY = debounce(() => {
        this.themeTopYs.push(0);
        this.themeTopYs.push(this.$refs.params.$el.offsetTop);
        this.themeTopYs.push(this.$refs.comments.$el.offsetTop);
        this.themeTopYs.push(this.$refs.recommends.$el.offsetTop);
        this.themeTopYs.push(Number.MAX_VALUE)
        // console.log(this.themeTopYs)
      },500)
    },
    mounted() {
    },
    destroyed() {
      this.$bus.$off('itemImageLoad',this.itemImgListener)
    }
  }
</script>

<style scoped>
  #detail {
    position: relative;
    z-index: 10;
    background-color: #fff;
    height: 100vh;
  }

  .detail-nav {
    position: relative;
    background-color: #fff;
    z-index: 10;
  }

  .content {
    height: calc(100% - 44px - 49px);
  }
</style>
