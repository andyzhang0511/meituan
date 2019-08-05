<template>
  <div class="nav">
    <router-link class="nav-item" to="/goods">
      品类筛选
      <i class="line"></i>
    </router-link>
    <router-link class="nav-item" to="/ratings">
      商品评价({{commentNum111}})
      <i class="line"></i>
    </router-link>
    <router-link class="nav-item" to="/seller">
      线下门店
      <i class="line"></i>
    </router-link>
  </div>
</template>

<script>
export default {
  props: {
    commentNum111: {
      type: Number,
      default: 0
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.nav {
  display: flex;
  widows: 100%;
  height: 40px;
  line-height: 40px;
  border-bottom: 1px solid #e4e4e4;
}
.nav-item {
  flex: 1;
  text-align: center;
  font-size: 13px;
  text-decoration: none;
  color: #666666;
  position: relative;
  /* background: pink; */
}
/* 选中的样式  需要在main.js里设置 */
.nav .active {
  color: #ffbb22;
}
.nav .active .line {
  width: 20px;
  height: 2px;
  display: inline-block;
  background: #ffbb22;
  position: absolute;
  left: 50%;
  /* 居中 left: 50%; */
  bottom: 0;
  margin-left: -10px;

  /* margin-left为负值像素，偏移值相对于自身，其后元素受影响 */
}
</style>
