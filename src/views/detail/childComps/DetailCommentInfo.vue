<template>
  <div v-if="Object.keys(commentInfo).length !== 0" class="comment-info">
    <div class="info-header">
      <div class="header-title">用户评价</div>
      <div class="header-more">更多</div>
    </div>
    <div class="info-user">
      <img :src="commentInfo.user.avatar" alt="">
      <span>{{commentInfo.user.uname}}</span>
    </div>
    <div class="info-detail">
      <p>{{commentInfo.content}}</p>
      <div class="info-other">
        <span class="date">{{commentInfo.created | showDate}}</span>
        <span>{{commentInfo.style}}</span>
      </div>
      <div class="info-imgs">
        <img v-for="(item,index) in commentInfo.images" :src="item" :key="index" alt="">
      </div>
    </div>
  </div>
</template>

<script>
  import {formatDate} from "common/utils";

  export default {
    name: "DetailCommentInfo",
    props: {
      commentInfo: {
        type: Object,
        default() {
          return {}
        }
      }
    },
    filters: {
      showDate(value) {
        // 1.将时间戳转成Date对象
        const date = new Date(value * 1000)

        // 2.将date进行格式化
        return formatDate(date, 'yyyy-MM-dd')
      }
    }
  }
</script>

<style scoped>
  .comment-info {
    padding: 0 15px;
  }

  .info-header {
    height: 44px;
    line-height: 44px;
    border-bottom: 2px solid #f2f5f8;
  }

  .info-header .header-title {
    float: left;
  }

  .info-header .header-more {
    float: right;
  }

  .info-user {
    padding: 8px 0 20px 0;
    /*图文垂直居中*/
    display: flex;
    align-items: center;
  }

  .info-user img {
    width: 45px;
    height: 45px;
    border-radius: 50%;
    border: 1px solid rgba(100,100,100,.1);
    margin-right: 8px;
  }

  .info-detail p {
    font-size: 15px;
  }

  .info-detail .info-other {
    margin-top: 15px;
    font-size: 13px;
    color: #999999;
  }

  .info-other .date {
    margin-right: 10px;
  }

  .info-imgs {
    margin-top: 15px;
  }

  .info-imgs img {
    width: 70px;
    height: 70px;
    margin-right: 5px;
  }
</style>
