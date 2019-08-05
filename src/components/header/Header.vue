<template>
  <div class="header">
    <!-- {{poiInfo111.name}} -->
    <!-- 顶部通栏 开始 -->
    <div class="top-wrapper">
      <!-- 后退箭头 -->
      <div class="back-wrapper">
        <span class="icon-arrow_lift"></span>
      </div>

      <!-- 搜索框 -->
      <form class="search-wrapper">
        <span class="search-icon"></span>
        <input class="search-bar" type="text" placeholder="搜索店内商品" />
      </form>

      <!-- 拼单模块 -->
      <div class="more-wrapper">
        <a class="spelling-bt" href="#">拼单</a>
        <div class="more-bt">
          <i class="s-radius"></i>
          <i class="s-radius"></i>
          <i class="s-radius"></i>
        </div>
      </div>
    </div>
    <!-- 顶部通栏 结束 -->

    <!-- 主题内容 开始 -->
    <div class="content-wrapper">
      <div class="icon" :style="head_bg">
      </div>
      <div class="name">
        <h3>{{poiInfo111.name}}</h3>
      </div>
      <div class="collect">
        <img src="./img/star.png" />
        <span>收藏</span>
      </div>
    </div>
    <!-- 主题内容 结束 -->

    <!-- 公告内容 开始 -->
    <div class="bulletin-wrapper">
      <!-- 当前是一个数组，有的有属性有的没有，所以需要对这个内容进行判断，预防当前的数组越界 -->
      <!-- v-if 判断poiInfo111下面是否有discounts2，如果有，则正常展示 -->
      <img class="icon" v-if="poiInfo111.discounts2" :src="poiInfo111.discounts2[0].icon_url" />
      <span class="text" v-if="poiInfo111.discounts2">{{poiInfo111.discounts2[0].info}}</span>
      <div class="detail" v-if="poiInfo111.discounts2" @click="showBulletin">
        {{poiInfo111.discounts2.length}}个活动
        <span class="icon-keyboard_arrow_right"></span>
      </div>
    </div>  
    <!-- 公告内容 结束 -->

    <!-- 背景内容 开始 -->
    <!-- <div class="bg-wrapper" :style='"background:url("+poiInfo111.head_pic_url+")"'> -->
    <div class="bg-wrapper" :style="head_pic_url">
      <!-- 注意此处的绑定问题 可以避免图片被拉伸  head_pic_url:头部图片 -->
      <!-- <img :src="poiInfo111.head_pic_url"> -->
    </div>
    <!-- 背景内容 结束 -->

    <!-- 公告详情 开始 -->
    <transition name="bulletin-detail">
      <div class="bulletin-detail" v-show="isShow">
        <div class="detail-wrapper">
          <!-- 打开内容容器 -->
          <div class="main-wrapper" :style="detail_bg">
            
            <div class="icon" :style="head_bg"></div>
            <h3 class="name">{{poiInfo111.name}}</h3>

            <!-- 星级评价 -->
            <div class="score">
              <!-- 将对应的值传过去 -->
              <app-star :score="poiInfo111.wm_poi_score"></app-star>
              <span>{{poiInfo111.wm_poi_score}}</span>
            </div>
            <p class="tip">
              {{poiInfo111.min_price_tip}}
              <i>|</i>
              {{poiInfo111.shipping_fee_tip}}
              <i>|</i>
              {{poiInfo111.delivery_time_tip}}
            </p>

            <p class="time">配送时间:{{poiInfo111.shipping_time}}</p>
          </div>

          <!-- 关闭内容容器 -->
          <div class="close-wrapper">
            <span class="icon-close" @click="closeBulletin"></span>
          </div>
        </div>
      </div>
    </transition>
    <!-- 公告详情 结束 -->
  </div>
</template>

<script>
import Star from "../star/Star.vue";
export default {
  data() {
    return {
      isShow: false
    };
  },
  components: {
    "app-star": Star
  },
  props: {
    poiInfo111: {
      type: Object,
      default: {}
    }
  },
  computed: {
    head_pic_url() {
      return "background-image:url(" + this.poiInfo111.head_pic_url + ");"; //头部背景图
    },
    head_bg() {
      return "background-image:url(" + this.poiInfo111.pic_url + ");"; //麦当劳背景图
    },
    detail_bg() {
      return "background-image:url(" + this.poiInfo111.poi_back_pic_url + ");"; //X个活动背景图
    }
  },
  methods: {
    showBulletin() {
      this.isShow = true;
    },
    closeBulletin() {
      this.isShow = false;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url(../../common/css/icon.css);
.header {
  height: 130px;
  padding-top: 20px;
}
/* 顶部通栏样式 */
.header .top-wrapper {
  position: relative;
}

/* 后退箭头盒子 */
.header .top-wrapper .back-wrapper {
  width: 50px;
  height: 31px;
  position: absolute;
  left: 0;
  top: 0;
  text-align: center;
  line-height: 31px;
}

/* 后退箭头盒子里的箭头 */
.header .top-wrapper .back-wrapper span {
  display: inline-block;
  color: white;
}

/* 搜索框盒子 */
.header .top-wrapper .search-wrapper {
  width: 100%;
  height: 31px;
  /* background: pink; */
  padding: 0 104px 0 50px;
  /* 设置盒子从边框开始计算  ???*/
  box-sizing: border-box;
}

/* 搜索框盒子里的搜索图标 */
.header .top-wrapper .search-wrapper .search-icon {
  width: 28px;
  height: 31px;
  background: url(./img/search.png) no-repeat center center;
  background-size: 13px 13px;
  position: absolute;
}

/* 搜索框 */
.header .top-wrapper .search-wrapper .search-bar {
  width: 100%;
  height: 31px;
  border: 0;
  /* 设置盒子从边框开始计算*/
  box-sizing: border-box;
  background: #cdcdcc;
  border-radius: 25px;
  padding-left: 28px;
  /* 去除选中时蓝色边框*/
  outline: none;
}

/* 拼单加三个点盒子 */
.header .top-wrapper .more-wrapper {
  width: 65px;
  height: 24px;
  background: orange;
  position: absolute;
  right: 0;
  top: 0;
  padding: 7px 15px 0 24px;
}

/* 拼单 */
.header .top-wrapper .more-wrapper .spelling-bt {
  width: 30px;
  height: 17px;
  color: white;
  line-height: 17px;
  border: 1px solid white;
  text-align: center;
  float: left;
  text-decoration: none;
  font-size: 10px;
}

/* 三个点盒子 */
.header .top-wrapper .more-wrapper .more-bt {
  float: right;
  width: 20px;
  height: 24px;
  margin-left: 13px;
  margin-top: 6px;
}

/* 三个点 */
.header .top-wrapper .more-wrapper .more-bt .s-radius {
  width: 3px;
  height: 3px;
  border-radius: 50%;
  border: 1px solid white;
  display: block;
  float: left;
  margin-right: 1px;
}

/* 顶部通栏背景图片 */
.header .bg-wrapper {
  width: 100%;
  height: 150px;
  position: absolute;
  left: 0;
  top: 0;
  z-index: -1;
  background-size: 100% 135%;
  background-position: center -12px;
  /* background-position: 0 0; */

}

/* 主题内容盒子 */
.header .content-wrapper {
  padding: 17px 10px 11px;
  height: 55px;
}

 /* 麦当劳图片 */
.header .content-wrapper .icon {
  width: 110px;
  height: 55px;
  /* background-size: 135% 100%; */
  background-position: center;
  border-radius: 5px;
  float: left;
}

/* 餐厅名字盒子 */
.header .content-wrapper .name {
  float: left;
  padding: 18px 0 0 12px;
}

/* 餐厅名字 */
.header .content-wrapper .name h3 {
  font-size: 16px;
  font-weight: bold;
  color: white;
}

/* 收藏盒子 */
.header .content-wrapper .collect {
  width: 25px;
  height: 37px;
  float: right;
  text-align: center;
  padding-top: 6px;
}

/* 收藏五角星 */
.header .content-wrapper .collect img {
  width: 20px;
  height: 20px;
}

/* 收藏文字 */
.header .content-wrapper .collect span {
  margin-top: 7px;
  color: white;
  font-size: 11px;
}

/* 公告内容盒子 */
.header .bulletin-wrapper {
  height: 16px;
  padding: 0 10px;
}

/* 公告内容--首 */
.header .bulletin-wrapper .icon {
  width: 16px;
  height: 16px;
  float: left;
  margin-right: 6px;
}

/* 公告内容--活动说明 */
.header .bulletin-wrapper .text {
  font-size: 11px;
  color: white;
  float: left;
  line-height: 16px;
}

/* 公告内容--1个活动盒子 */
.header .bulletin-wrapper .detail {
  color: white;
  float: right;
  font-size: 11px;
  line-height: 16px;
}

/* 公告内容--1个活动盒子 右箭头 */
.header .bulletin-wrapper .detail span {
  font-size: 16px;
  line-height: 16px;
  float: right;
}

/* 公告详情 样式 */

/* 公告详情 整体盒子背景图 */
.header .bulletin-detail {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
  background: rgba(98, 98, 98, 0.8);
  z-index: 999;
}

/* 公告详情 红色模块 */
.header .bulletin-detail .detail-wrapper {
  width: 100%;
  height: 100%;
  padding: 43px 20px 125px;
  box-sizing: border-box;
}

/* 弹出层主体 */
.header .bulletin-detail .detail-wrapper .main-wrapper {
  width: 100%;
  height: 100%;
  background-size: 100% 100%;
  border-radius: 10px;
  text-align: center;
}

/* 弹出层主体---麦当劳头像 */
.header .bulletin-detail .detail-wrapper .main-wrapper .icon {
  width: 60px;
  height: 60px;
  background-size: 135% 100%;
  background-position: center;
  border-radius: 5px;
  display: inline-block;
  margin-top: 40px;
}

/* 弹出层主体---餐厅名称 */
.header .bulletin-detail .detail-wrapper .main-wrapper .name {
  font-size: 15px;
  color: white;
  margin-top: 13px;
}

/* 弹出层主体---评价整体盒子 */
.header .bulletin-detail .detail-wrapper .main-wrapper .score {
  height: 10px;
  margin-top: 6px;
  /* text-align: center; */
  font-size: 0;
}

/* 弹出层主体---评价星星 */
.header .bulletin-detail .detail-wrapper .main-wrapper .score .star {
  display: inline-block;
  margin-right: 7px;
}

/* 弹出层主体---评价分值 */
.header .bulletin-detail .detail-wrapper .main-wrapper .score span {
  display: inline-block;
  font-size: 10px;
  color: white;
}

/* 起送 ¥0 | 配送 ¥9 | 30分钟 */
.header .bulletin-detail .detail-wrapper .main-wrapper .tip {
  font-size: 11px;
  color: #bababc;
  margin-top: 8px;
}

.header .bulletin-detail .detail-wrapper .main-wrapper .tip i {
  margin: 0 7px;
}

/* 配送时间 */
.header .bulletin-detail .detail-wrapper .main-wrapper .time {
  font-size: 11px;
  color: #bababc;
  margin-top: 13px;
}


.header .bulletin-detail .detail-wrapper .main-wrapper .discounts {
  margin-top: 20px;
  padding: 0 20px;
}

.header .bulletin-detail .detail-wrapper .main-wrapper .discounts p {
  padding-top: 20px;
  border-top: 1px solid #bababc;
}

.header .bulletin-detail .detail-wrapper .main-wrapper .discounts img {
  width: 16px;
  height: 16px;
  vertical-align: middle;
}

.header .bulletin-detail .detail-wrapper .main-wrapper .discounts span {
  font-size: 11px;
  line-height: 16px;
  color: white;
}

/* 关闭内容容器盒子 */
.header .bulletin-detail .detail-wrapper .close-wrapper {
  padding-top: 20px;
  height: 40px;
  text-align: center;
  
}

/* 关闭按钮 */
.header .bulletin-detail .detail-wrapper .close-wrapper span {
  width: 40px;
  height: 40px;
  line-height: 40px;
  border-radius: 50%;
  font-size: 14px;
  color: white;
  display: inline-block;
  background: rgba(118, 118, 118, 0.7);
  border: 1px solid rgba(140, 140, 140, 0.9);
}

/* 动画效果 */
.bulletin-detail-enter-active,
.bulletin-detail-leave-active {
  transition: 1s all;
}
.bulletin-detail-enter,
.bulletin-detail-leave-to {
  opacity: 0;
}
.bulletin-detail-enter-to,
.bulletin-detail-leave {
  opacity: 1;
}
</style>
