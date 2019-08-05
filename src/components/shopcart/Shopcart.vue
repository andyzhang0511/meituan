<template>
  <div class="shopcart">
    <div class="shopcart-wrapper">
      <!-- 底部左侧 -->
      <div class="content-left">
        <div class="logo-wrapper" :class="{'highlight':totalCount>0}">
          <span
            class="icon-shopping_cart logo"
            :class="{'highlight':totalCount>0}"
            @click="toggleList"
          ></span>
          <i class="num" v-show="totalCount">{{totalCount}}</i>
        </div>
        <div class="desc-wrapper">
          <p class="total-price" v-show="totalPrice">￥{{totalPrice}}</p>
          <p class="tip" :class="{'highlight':totalCount>0}">另需{{poiInfo333.shipping_fee_tip}}</p>
        </div>
      </div>
      <!-- 底部右侧 -->
      <div class="content-right" :class="{'highlight':totalCount>0}">{{payStr}}</div>


      <!-- 购物车列表 -->
      <div class="shopcart-list" v-show="listshow" :class="{'show':listshow}">
        <!-- 顶部 -->
        <div class="list-top" 
        v-if="poiInfo333.discounts2">{{poiInfo333.discounts2[0].info}}</div>

        <div class="list-header">
          <h3 class="title">1号口袋</h3>
          <div class="empty" @click="clearAll">
            <img src="./img/ash_bin.png">
            <span>清空购物车</span>
          </div>
        </div>

        <!-- 中部 -->
        <div class="list-content" ref="listContent">
          <ul>
            <li class="food-item" v-for="(food,index) in selectFoods111" :key="index">
              <div class="desc-wrapper">
                <div class="desc-left">
                  <p class="name">{{food.name}}</p>
                  <p class="unit" v-show="!food.description">{{food.unit}}</p>
                  <p class="description" v-show="!food.unit">{{food.description}}</p>
                </div>
                <div class="desc-right">￥{{food.min_price}}</div>
              </div>
              <div class="cartcontrol-wrapper">
                <app-cart-control :food111="food"></app-cart-control>
              </div>
            </li>
          </ul>
        </div>

        <!-- 底部 -->
        <div class="list-bottom"></div>
      </div>
    </div>
    <div class="shopcart-mask" v-show="listshow" @click="hideMask"></div>
  </div>
</template> 

<script>  
import CartControl from "../cartcontrol/CartControl";
import BScroll from "better-scroll";//引入滚动


export default {
  data() {
    return {
      fold: true
    };
  },
  props: {
    poiInfo333: {
      type: Object,
      default: {}
    },
    selectFoods111: {
      type: Array,
      default() {
        return [];
      }
    }
  },

  computed: {
    totalCount() {
      let num = 0;
      this.selectFoods111.forEach(food => {
        num += food.count;
      });
      return num;
    },
    totalPrice() {
      let total = 0;
      this.selectFoods111.forEach(food => {
        total += food.min_price * food.count;
      });
      return total;
    },
    payStr() {
      if (this.totalCount > 0) {
        return "去结算";
      } else {
        return this.poiInfo333.min_price_tip;
      }
    },
    listshow() {
      //购物车totalCount为0时不要展现
      if (!this.totalCount) {
        this.fold = true  //折叠为真
        return false      //如果是折叠的话就不展示，即v-show="listshow" => v-show="false"   'show':listshow => 'show':false
      }
      //否则购物车totalCount不为0时需要展现,不展现就要撑开
      let show = !this.fold  //show为true  fold因为toggle把它改成了false

      //!this.fold即不再折叠，购物车展开状态为真
      if (show) {
        this.$nextTick(() => {
          if (!this.shopScroll) {  //没有实例化之前 !this.shopScroll最开始是没有的
            this.shopScroll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            //已经有实例化了,当页面已经发生变化之后,会在当前计算属性computed中找到，页面/数据已经发生变化了，然后refresh刷新
            this.shopScroll.refresh()
          }
        })
      }
      return show   //v-show="listshow" => v-show="true"   'show':listshow => 'show':true

      // 也可以写成以下方式
      // return !this.fold 
    }
  },
  methods: {
    //点击购物车logo切换购物车list折叠&展开
    // 判断购物车个数是否为空
    toggleList() {
      //购物车totalCount为0时点击中断，也就是说点着没反应
      if (!this.totalCount) {
        return
      }
      //购物车totalCount不为0时，fold等于false，不折叠,也就是说要展开了
      //展开后再次点击，fold就等于true,要折叠了
      this.fold = !this.fold
    },
    clearAll() {
      this.selectFoods111.forEach(food => {
        food.count = 0;
      })
    },
    hideMask() {
      this.fold = true
    }

  },
  components: {
    "app-cart-control": CartControl
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url(../../common/css/icon.css);

/* 底部大盒子 */
.shopcart-wrapper {
  width: 100%;
  height: 51px;
  background: #514f4f;
  position: fixed;
  left: 0;
  bottom: 0;
  display: flex;
  z-index: 99;
}
/* 底部大盒子--左部 */
.shopcart-wrapper .content-left {
  /* flex为1的时候，根据当前屏幕的宽度来响应 */
  flex: 1;
}
/* 底部大盒子--右部分 */
.shopcart-wrapper .content-right {
  /* flex为0的时候，宽度不会变化，宽度给的110px */
  flex: 0 0 110px;
  font-size: 15px;
  color: #bab9b9;
  line-height: 51px;
  text-align: center;
  font-weight: bold;
}
/* 底部大盒子--左部分--圆盒子 */
.shopcart-wrapper .content-left .logo-wrapper {
  width: 50px;
  height: 50px;
  background: #666666;
  border-radius: 50%;
  position: relative;
  top: -14px;
  left: 10px;
  text-align: center;
  float: left;
}
/* 底部大盒子--左部分--圆盒子--购物车logo */
.shopcart-wrapper .content-left .logo-wrapper .logo {
  font-size: 28px;
  color: #c4c4c4;
  line-height: 50px;
}
/* 底部大盒子--左部分--圆盒子--￥5.5 */
.shopcart-wrapper .content-left .desc-wrapper {
  float: left;
  margin-left: 13px;
}
/* 5.5 */
.shopcart-wrapper .content-left .desc-wrapper .total-price {
  font-size: 18px;
  line-height: 33px;
  color: white;
}
/* 另需配送 */
.shopcart-wrapper .content-left .desc-wrapper .tip {
  font-size: 12px;
  color: #bab9b9;
  line-height: 51px;
}

/* 底部大盒子-圆盒子-高亮显示 */
.shopcart-wrapper .content-left .logo-wrapper.highlight {
  background: #ffd161;
}
/* 底部大盒子-圆盒子-logo-高亮显示 */
.shopcart-wrapper .content-left .logo-wrapper .logo.highlight {
  color: #2d2b2a;
}
/* 底部大盒子-圆盒子-右上角小圆 */
.shopcart-wrapper .content-left .logo-wrapper .num {
  width: 15px;
  height: 15px;
  line-height: 15px;
  border-radius: 50%;
  font-size: 9px;
  color: white;
  background: red;
  position: absolute;
  right: 0;
  top: 0;
}
.shopcart-wrapper .content-left .desc-wrapper .tip.highlight {
  line-height: 12px;
}
/* 去结算盒子点亮 */
.shopcart-wrapper .content-right.highlight {
  background: #ffd161;
  color: #2d2b2a;
}


/* 购物车折叠样式 */
.shopcart-wrapper .shopcart-list {
  position: absolute;
  left: 0;
  top: 0;
  z-index: -1;
  width: 100%;
}

.shopcart-wrapper .shopcart-list.show {
  transform: translateY(-100%);
}

.shopcart-wrapper .shopcart-list .list-top {
  height: 30px;
  text-align: center;
  font-size: 11px;
  background: #f3e6c6;
  line-height: 30px;
  color: #646158;
}

.shopcart-wrapper .shopcart-list .list-header {
  height: 30px;
  background: #f4f4f4;
}
.shopcart-wrapper .shopcart-list .list-header .title {
  float: left;
  border-left: 4px solid #53c123;
  padding-left: 6px;
  line-height: 30px;
  font-size: 12px;
}
.shopcart-wrapper .shopcart-list .list-header .empty {
  float: right;
  line-height: 30px;
  margin-right: 10px;
  font-size: 0;
}
.shopcart-wrapper .shopcart-list .list-header .empty img {
  height: 14px;
  margin-right: 9px;
  vertical-align: middle;
}
.shopcart-wrapper .shopcart-list .list-header .empty span {
  font-size: 12px;
  vertical-align: middle;
}

.shopcart-wrapper .shopcart-list .list-content {
  max-height: 360px;
  overflow: hidden;
  background: white;
}
.shopcart-wrapper .shopcart-list .list-content .food-item {
  height: 38px;
  padding: 12px 12px 10px 12px;
  border-bottom: 1px solid #f4f4f4;
}
.shopcart-wrapper .shopcart-list .list-content .food-item .desc-wrapper {
  float: left;
  width: 240px;
}
.shopcart-wrapper
  .shopcart-list
  .list-content
  .food-item
  .desc-wrapper
  .desc-left {
  float: left;
  width: 170px;
}
.shopcart-wrapper
  .shopcart-list
  .list-content
  .food-item
  .desc-wrapper
  .desc-left
  .name {
  font-size: 16px;
  margin-bottom: 8px;

  /* 超出部分隐藏*/
  -webkit-line-clamp: 1;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
  height: 16px;
}
.shopcart-wrapper
  .shopcart-list
  .list-content
  .food-item
  .desc-wrapper
  .desc-left
  .unit {
  font-size: 12px;
  color: #b4b4b4;
}
.shopcart-wrapper
  .shopcart-list
  .list-content
  .food-item
  .desc-wrapper
  .desc-left
  .description {
  font-size: 12px;
  color: #b4b4b4;

  /* 超出部分隐藏*/
  overflow: hidden;
  height: 12px;
}
.shopcart-wrapper
  .shopcart-list
  .list-content
  .food-item
  .desc-wrapper
  .desc-right {
  float: right;
  width: 70px;
  text-align: right;
}
.shopcart-wrapper
  .shopcart-list
  .list-content
  .food-item
  .desc-wrapper
  .desc-right
  .price {
  font-size: 12px;
  line-height: 38px;
}

.shopcart-wrapper .shopcart-list .list-content .food-item .cartcontrol-wrapper {
  float: right;
  margin-top: 6px;
}

/* 购物车蒙版 */
.shopcart .shopcart-mask{
	position: fixed;
	top: 0;
	right: 0;
	width: 100%;
	height: 100%;
	z-index: 98;
	background: rgba(7,17,27,0.6);
}
</style>
