<template>
  <div class="cartcontrol">
    <transition name="move">
      <div
        @click.stop.prevent="decreaseCart"
        v-show="food111.count"
        class="cart-decrease icon-remove_circle_outline"
      ></div>
    </transition>
    <div v-show="food111.count" class="cart-count">{{food111.count}}</div>
    <div @click.stop.prevent="increaseCart" class="cart-add icon-add_circle">
      <i class="bg"></i>
    </div>
  </div>
</template>

<script>
import Vue from "vue"; //全局api
export default {
  props: {
    food111: {
      type: Object
    }
  },
  methods: {
    decreaseCart() {
      this.food111.count--;
    },
    increaseCart() {
      if (!this.food111.count) {
        Vue.set(this.food111, "count", 1);
      } else {
        this.food111.count++;
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url(../../common/css/icon.css);

.cartcontrol {
  font-size: 0;
}

/* - */
.cartcontrol .cart-decrease {
  display: inline-block;
  width: 26px;
  height: 26px;
  font-size: 26px;
  color: #b4b4b4;
}

.cartcontrol .cart-add .bg {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: black;
  position: absolute;
  left: 3px;
  top: 3px;
  z-index: -1;
}
/* 1 */
.cartcontrol .cart-count {
  display: inline-block;
  width: 25px;
  text-align: center;
  font-size: 12px;
  line-height: 26px;
  vertical-align: top;
}
/* + */
.cartcontrol .cart-add {
  display: inline-block;
  width: 26px;
  height: 26px;
  font-size: 26px;
  color: #ffd161;
  position: relative;
}

.move-enter-active,.move-leave-active{
  transition: all 0.5s linear;
}

.move-enter,.move-leave-to{
  transform: translateX(20px) rotate(180deg)
}
</style>
 