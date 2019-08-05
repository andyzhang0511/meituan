<template>
  <div id="app">
    <!-- 头部 -->
    <app-header :poiInfo111="poiInfo"></app-header>

    <!-- 导航 -->
    <app-nav :commentNum111="commentNum"></app-nav>

    <!-- 展示导航里的每个路由对应的内容 -->
    <!-- 主要用于保留组件状态或避免重新渲染 -->
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
  </div>
</template>

<script>
import Header from "./components/header/Header";
import Nav from "./components/nav/Nav";

export default {
  name: "App",
  components: {
    "app-header": Header,
    "app-nav": Nav
  },
  data() {
    return {
      poiInfo: {},
      commentNum: 0
    };
  },
  created() {
    // 请求goods
    // fetch("https://www.easy-mock.com/mock/5d172ec29e7e670ab45bf987/example/api/goods")
    fetch("/api/goods")
      .then(res => {
        return res.json(); //将当前数据进行转化
      })
      .then(response => {
        console.log(response)
        if (response.code == 0) {
          this.poiInfo = response.data.poi_info;
          // console.log(this.poiInfo);
        }
      });

    //请求ratings
    fetch("/api/ratings")
      .then(res => {
        return res.json(); //将当前数据进行转化
      })
      .then(response => {
        // console.log(response)
        if (response.code == 0) {
          this.commentNum = response.data.comment_num;
          // console.log(this.commentNum);
        }
      });
  }
};
</script>

<style>
</style>
