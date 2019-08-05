<template>
  <transition name="food-detail">
    <div class="food" ref="foodView" v-show="showFlag">
      <div class="food-wrapper">
        <div class="food-content">
          <div class="img-wrapper">
            <img class="food-img" :src="selectFood111.picture">
            <span @click="closeView" class="close-bt icon-close"></span>
            <img class="share-bt" src="./img/share.png">
            <img class="more-bt" src="./img/more.png">
          </div>

          <div class="content-wrapper">
            <h3 class="name">{{selectFood111.name}}</h3>
            <p class="saled">{{selectFood111.month_saled_content}}</p>
            <img
              class="product"
              v-show="selectFood111.product_label_picture"
              :src="selectFood111.product_label_picture"
            >
            <p class="price">
              <span class="text">￥{{selectFood111.min_price}}</span>
              <span class="unit">/{{selectFood111.unit}}</span>
            </p>

            <div class="cartcontrol-wrapper">
              <CartControl :food111="selectFood111"></CartControl>
            </div>
            <div
              class="buy"
              @click="addProduct"
              v-show="!selectFood111.count||selectFood111.count==0"
            >选规格</div>
          </div>
        </div>

        <!-- 分隔线 -->
        <Split></Split>

        <!-- 外卖评价 -->
        <div class="rating-wrapper">
          <!-- 评价头部 -->
          <div class="rating-title">
            <!-- 二级选属性，需用v-if -->
            <div class="like-ratio" v-if="selectFood111.rating">
              <span class="titie">{{selectFood111.rating.title}}</span>
              <span class="ratio">
                (
                {{selectFood111.rating.like_ratio_desc}}
                <i>{{selectFood111.rating.like_ratio}}</i>
                )
              </span>
            </div>
            <div class="snd-title" v-if="selectFood111.rating">
              <span class="text">{{selectFood111.rating.snd_title}}</span>
              <span class="icon icon-keyboard_arrow_right"></span>
            </div>
          </div>

          <!-- 评价列表内容 -->
          <ul class="rating-content" v-if="selectFood111.rating">
            <li
              v-for="(comment,index) in selectFood111.rating.comment_list"
              :key="index"
              class="comment-item"
            >
              <!-- 图标 -->
              <div class="comment-header">
                <img :src="comment.user_icon" v-if="comment.user_icon">
                <img src="./img/anonymity.png" v-if="!comment.user_icon">
              </div>

              <!-- 评论主体内容 -->
              <div class="comment-main">
                <div class="user">{{comment.user_name}}</div>
                <div class="time">{{comment.comment_time}}</div>
                <div class="content">{{comment.comment_content}}</div>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import BScroll from "better-scroll";

import CartControl from "../cartcontrol/CartControl";
import Split from "../split/Split";
import Vue from 'vue'
export default {
  props: {
    selectFood111: {
      type: Object
    }
  },
  data() {
    return {
      showFlag: false
    };
  },
  methods: {
    // 父级调用子级的方法  也就是Goods.vue调用子级的方法
    showView() {
      this.showFlag = true;

      // 一进来的时候就拥有滚动页面的能力
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.foodView, {
            click: true
          })
        } else {
          this.scroll.refresh()//scroll.refresh() 方法重新计算来确保滚动效果的正常。
        }
      })

    },
    closeView() {
      this.showFlag = false;
    },
    addProduct() {
      //只要一点击，当前的商品就拥有一个count,一旦为1就不再显示“选规格”模块了
      Vue.set(this.selectFood111, "count", 1)

      // Vue.set( target, propertyName/index, value )
      // target:   {Object | Array} 
      // propertyName/index:  {string | number}
      // value {any} 
      // 向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，且触发视图更新。
      // 注意对象不能是 Vue 实例，或者 Vue 实例的根数据对象。
    }
  },
  components: {
    CartControl,
    Split
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url(../../common/css/icon.css);

.food {
  position: fixed;
  left: 0;
  top: 0;
  bottom: 51px;
  background: white;
  width: 100%;
  z-index: 90;
}
.food-detail-enter-active,
.food-detail-leave-active {
  transition: 1s;
}
.food-detail-enter,
.food-detail-leave-to {
  transform: translateX(100%);
}
.food .food-wrapper .food-content {
}
.food .food-wrapper .food-content .img-wrapper {
  position: relative;
  width: 100%;
  height: 0;
  padding-top: 100%;
  /* background: pink; */
}
.food .food-wrapper .food-content .img-wrapper .food-img {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
}
.food .food-wrapper .food-content .img-wrapper .close-bt {
  width: 30px;
  height: 30px;
  position: absolute;
  left: 10px;
  top: 10px;
  text-align: center;
  font-size: 30px;
  color: white;
  background: #7f7f7f;
  border-radius: 50%;
}
.food .food-wrapper .food-content .img-wrapper .share-bt,
.food .food-wrapper .food-content .img-wrapper .more-bt {
  width: 30px;
  height: 30px;
  position: absolute;
  top: 10px;
}
.food .food-wrapper .food-content .img-wrapper .share-bt {
  right: 50px;
}
.food .food-wrapper .food-content .img-wrapper .more-bt {
  right: 10px;
}

.food .food-wrapper .food-content .content-wrapper {
  padding: 0 0 16px 16px;
  position: relative;
}
.food .food-wrapper .food-content .content-wrapper .name {
  font-size: 15px;
  color: #333333;
  line-height: 30px;
  font-weight: bold;
}
.food .food-wrapper .food-content .content-wrapper .saled {
  font-size: 11px;
  color: #9d9d9d;
  margin-bottom: 6px;
}
.food .food-wrapper .food-content .content-wrapper .product {
  height: 15px;
  margin-bottom: 16px;
}
.food .food-wrapper .food-content .content-wrapper .price {
  font-size: 0;
}
.food .food-wrapper .food-content .content-wrapper .price .text {
  font-size: 17px;
  color: #fb4e44;
}
.food .food-wrapper .food-content .content-wrapper .price .unit {
  font-size: 11px;
  color: #9d9d9d;
}
.food .food-wrapper .food-content .cartcontrol-wrapper {
  position: absolute;
  right: 12px;
  bottom: 10px;
  padding: 2px;
}
.food .food-wrapper .food-content .buy {
  width: 64px;
  height: 30px;
  font-size: 12px;
  line-height: 30px;
  text-align: center;
  background: #ffd161;
  border-radius: 30px;
  position: absolute;
  right: 12px;
  bottom: 10px;
}

/* 评价部分 */
.food .food-wrapper .rating-wrapper {
  padding-left: 16px;
}
.food .food-wrapper .rating-wrapper .rating-title {
  padding: 16px 16px 16px 0;
}
.food .food-wrapper .rating-wrapper .rating-title .like-ratio {
  float: left;
  font-size: 0;
}
.food .food-wrapper .rating-wrapper .rating-title .like-ratio .title {
  font-size: 13px;
}
.food .food-wrapper .rating-wrapper .rating-title .like-ratio .ratio {
  font-size: 11px;
}
.food .food-wrapper .rating-wrapper .rating-title .like-ratio .ratio i {
  color: #fb4e44;
  font-size: 11px;
}

.food .food-wrapper .rating-wrapper .rating-title .snd-title {
  float: right;
  font-size: 0;
}
.food .food-wrapper .rating-wrapper .rating-title .snd-title .text,
.food .food-wrapper .rating-wrapper .rating-title .snd-title .icon {
  font-size: 13px;
  color: #9d9d9d;
  display: inline-block;
}
.food .food-wrapper .rating-wrapper .rating-title .snd-title .icon {
  margin-left: 12px;
}

.food .food-wrapper .rating-wrapper .comment-item {
  padding: 15px 14px 17px 0;
  border-bottom: 1px solid #f4f4f4;
  width: 100%;
  box-sizing: border-box;
  display: flex;
}
.food .food-wrapper .rating-wrapper .comment-item .comment-header {
  flex: 0 0 41px;
  margin-right: 10px;
}
.food .food-wrapper .rating-wrapper .comment-item .comment-header img {
  width: 41px;
  height: 41px;
  border-radius: 50%;
}

.food .food-wrapper .rating-wrapper .comment-item .comment-main {
  flex: 1;
  margin-top: 6px;
}
.food .food-wrapper .rating-wrapper .comment-item .comment-main .user {
  width: 50%;
  float: left;
  font-size: 12px;
  color: #333333;
}
.food .food-wrapper .rating-wrapper .comment-item .comment-main .time {
  width: 50%;
  float: right;
  text-align: right;
  font-size: 10px;
  color: #666666;
}
.food .food-wrapper .rating-wrapper .comment-item .comment-main .content {
  margin-top: 17px;
  font-size: 13px;
  line-height: 19px;
}
</style>
