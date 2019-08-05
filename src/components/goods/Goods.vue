<template>
  <div class="goods">
    <!-- 分类列表 -->
    <div class="menu-wrapper" ref="menuScroll">
      <ul>
        <!-- 专场 -->

        <!-- 绑定一个class ===0的话就有current这个样式，不===0的话就没有current这个样式 -->
        <li class="menu-item" 
        :class="{'current':currentIndex===0}"
         @click="selectMenu(0)">
          <p class="text">
            <img class="icon" :src="container.tag_icon" v-if="container.tag_icon">
            {{container.tag_name}}
          </p>
        </li>

        <li
          class="menu-item"
          :class="{'current':currentIndex===index+1}"
          v-for="(item,index) in goods"
          :key="index"
          @click="selectMenu(index+1)"
        >
          <p class="text">
            <img class="icon" :src="item.icon" v-if="item.icon">
            {{item.name}}
          </p>
          <i class="num" v-show="calculateCount(item.spus)">{{calculateCount(item.spus)}}</i>
        </li>
      </ul>
    </div>

    <!-- 商品列表 -->
    <div class="foods-wrapper" ref="foodScroll">
      <ul>
        <!-- 专场对应的两张图片 -->
        <li class="container-list food-list-hook">
          <div v-for="(item,index) in container.operation_source_list" :key="index">
            <img :src="item.pic_url">
          </div>
        </li>

        <!-- 具体分类 -->
        <li v-for="(item,index) in goods" :key="index" class="food-list food-list-hook">
          <!-- 热销/早餐/促销  ... -->
          <h3 class="title">{{item.name}}</h3>

          <!-- 具体的商品列表信息 -->
          <ul>
            <li
              v-for="(food,index) in item.spus"
              :key="index"
              @click="showDetail(food)"
              class="food-item"
            > 
              <!-- 热销对应的图片 -->
              <div class="icon" :style="head_bg(food.picture)"></div>

              <!-- 热销里面的内容 -->
              <div class="content">
                <h3 class="name">{{food.name}}</h3>
                <p class="desc" v-if="food.description">{{food.description}}</p>
                <div class="extra">
                  <span class="saled">{{food.month_saled_content}}</span>
                  <span class="praise">{{food.praise_content}}</span>
                </div>
                <img class="product" :src="food.product_label_picture">
                <p class="price">
                  <span class="text">${{food.min_price}}</span>
                  <span class="unit">/{{food.unit}}</span>
                </p>
              </div>

              <!-- 右边的加入购物车模块 -->
              <div class="cartcontrol-wrapper">
                <!-- 遍历出来的food传递到cartControl中去 -->
                <app-cart-control :food111="food"></app-cart-control>
              </div>

            </li>
          </ul>
        </li>
      </ul>
    </div>

    <!-- 购物车 -->
    <app-shopcart :poiInfo333="poiInfo222" :selectFoods111="selectFoods"></app-shopcart>

    <!-- 商品详情 -->
    <!-- 父级调用子级  ref ???-->
    <app-product-detail 
    :selectFood111="selectFood" ref="foodView111">
    </app-product-detail>
  </div>
</template>

<script>
import BScroll from "better-scroll";
import Shopcart from "../shopcart/Shopcart";
import CartControl from "../cartcontrol/CartControl";
import ProductDetail from "../productDetail/ProductDetail";

export default {
  data() {
    return {
      container: {},
      goods: [],
      poiInfo222: {},
      listHeight: [],
      menuScroll: {},
      foodScroll: {},
      selectFood:{},
      scrollY: 0
    };
  },

  //计算属性是不能接收参数的
  methods: {
    head_bg(imgName) {
      return "background-image:url(" + imgName + ");";
    },
    initScroll() {
      this.menuScroll = new BScroll(this.$refs.menuScroll,{
        click:true
      });
      this.foodScroll = new BScroll(this.$refs.foodScroll, {
        probeType: 3,
        click: true
      });

      //foodScroll 监听scroll事件
      this.foodScroll.on("scroll", pos => {
        // console.log(pos.y);

        this.scrollY = Math.abs(Math.round(pos.y));
        // console.log(this.scrollY)
        // Math.round最接近的整数 Math.abs 返回指定数字的绝对值
      });
    },
    calculateHeight() {
      //获取元素
      let foodList = this.$refs.foodScroll.getElementsByClassName(
        "food-list-hook"
      );
      // console.log(foodList)
 
      let height = 0;
      this.listHeight.push(height);

      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]; //累加
        height += item.clientHeight; //clientHeight  ???
        this.listHeight.push(height);
      }
      // console.log(this.listHeight);
    },
    selectMenu(index) {
      // console.log(index);
      let foodList = this.$refs.foodScroll.getElementsByClassName(
        "food-list-hook"
      );
      let element = foodList[index];
      // console.log(element);
      //滚动到对应元素的位置 
      // scrollToElement(element, 250) 两个参数（滚动到哪个参数，滚动的时间是多少）
      this.foodScroll.scrollToElement(element, 250);
    },
    calculateCount(spus) {
      let count = 0;
      spus.forEach(food => {
        if (food.count > 0) {
          count += food.count;
        }
      });
      return count
    },
    showDetail(food) {
      console.log(food);
      this.selectFood = food
      // console.log(123)
      this.$refs.foodView111.showView()//接收ProductDetail传过来的showView方法
    }
  },
  created() {
    // fetch("https://www.easy-mock.com/mock/5d172ec29e7e670ab45bf987/example/api/goods")
    fetch("/api/goods")
      .then(res => {
        return res.json(); //将当前数据进行转化
      })
      .then(response => {
        // console.log(response);
        if (response.code == 0) {
          this.container = response.data.container_operation_source;
          this.goods = response.data.food_spu_tags;
          this.poiInfo222 = response.data.poi_info;
          // console.log(this.container);    
          // console.log(this.goods);
          console.log(this.poiInfo222);

          //DOM已经更新
          this.$nextTick(() => {
            // 执行滚动方法
            this.initScroll();

            //计算分类的区间高度
            this.calculateHeight();
            //监听滚动的位置
            //根据滚动位置确认下标，与左侧对应
            //通过下标实现点击左侧，滚动右侧
          });
        }
      });
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        //获取商品区间的范围
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];

        //是否在上述区间中 !height2预防越界的问题 数组最后一个元素会涉及到越界问题 
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          // console.log(i);  
          return i;
        }
      }
      return 0;
    },

    //selectFoods()用来监听数据里的foods是否有变化，有变化就向Shopcart组件传数据
    selectFoods() {
      let foods = [];

      //迭代性函数
      this.goods.forEach(myfoods => {
        myfoods.spus.forEach(food => {
          if (food.count > 0) {
            foods.push(food);
          }
        });
      });
      return foods;
    }
  },
  components: {
    "app-shopcart": Shopcart,
    "app-cart-control": CartControl,
    "app-product-detail":ProductDetail
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.goods {
  display: flex;
  position: absolute;
  top: 190px;
  bottom: 51px;
  overflow: hidden;
  width: 100%;
  /* background-color: yellow; */
}
.goods .menu-wrapper {
  /* 0 0 不要动  宽度85px */
  flex: 0 0 85px;
  background: #f4f4f4;
  text-align: center;
}
.goods .foods-wrapper {
  /* flex为1的时候，拉伸的时候会变宽   为0的*/
  flex: 1;
  /* flex: 0; */ 
  /* background: blue; */
}

/* Menu item */

/* 专场及下面的每个小盒子 */
.goods .menu-wrapper .menu-item {
  padding: 16px 23px 15px 10px;
  border-bottom: 1px solid #e4e4e4;
  position: relative;
}

/* 专场icon */
.goods .menu-wrapper .menu-item .text .icon {
  width: 15px;
  height: 15px;
  vertical-align: middle;

  /* vertical-align: middle; */
  /* 把此元素放置在父元素的中部 */
}

/* 专场文字 */
.goods .menu-wrapper .menu-item .text {
  font-size: 13px;
  color: #333333;
  line-height: 17px;
  vertical-align: middle;

  -webkit-line-clamp: 2;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;

/* -webkit-line-clamp超过两行就出现省略号，
它需要组合其他外来的WebKit属性。常见结合属性：

display: -webkit-box; 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
-webkit-box-orient 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。
text-overflow，可以用来多行文本的情况下，用省略号“...”隐藏超出范围的文本 。 */

}

/* 商品列表 */
.goods .foods-wrapper .container-list {
  padding: 11px 11px 0 11px;
  border-bottom: 1px solid #e4e4e4;
}

/* 专场对应的两张图片 */
.goods .foods-wrapper .container-list img {
  width: 100%;
  margin-bottom: 11px;
  border-radius: 5px;
}

/* 具体分类商品布局 */
.goods .foods-wrapper .food-list {
  padding: 11px;
}
/* 热销/早餐/促销  文字+左边的橙色竖线 */
.goods .foods-wrapper .food-list .title {
  height: 13px;
  font-size: 13px;
  background: url(./img/btn_yellow_highlighted@2x.png) no-repeat left center;
  background-size: 2px 10px;
  padding-left: 7px;
  margin-bottom: 12px;
}
/* 热销/早餐/促销  下面对应的大块内容 */
.goods .foods-wrapper .food-list .food-item {
  display: flex;
  margin-bottom: 25px;
  position: relative;
}
/* 热销/早餐/促销  下面对应的大块内容  食物图片 */
.goods .foods-wrapper .food-list .food-item .icon {
  flex: 0 0 63px;
  background-position: center;
  background-size: 120% 100%;
  background-repeat: no-repeat;
  margin-right: 11px;
  height: 75px;
}
/* 热销里面的内容 */
.goods .foods-wrapper .food-list .food-item .content {
  flex: 1;
}

/* 热销里面的内容---食物名字 */
.goods .foods-wrapper .food-list .food-item .content .name {
  font-size: 16px;
  line-height: 21px;
  color: #333333;
  margin-bottom: 10px;
  padding-right: 27px;
}
/* 热销里面的内容---食物名字详细 */
.goods .foods-wrapper .food-list .food-item .content .desc {
  font-size: 10px;
  line-height: 19px;
  color: #bfbfbf;
  margin-bottom: 8px;

  /* 超出部分显示省略号*/
  -webkit-line-clamp: 1;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* 月售***  赞*** 大盒子 */
.goods .foods-wrapper .food-list .food-item .content .extra {
  font-size: 10px;
  color: #bfbfbf;
  margin-bottom: 7px;
}
/* 月售***  赞*** 大盒子--月售 */
.goods .foods-wrapper .food-list .food-item .content .extra .saled {
  margin-right: 14px;
}
/* 月售***  赞*** 大盒子--网友推荐 */
.goods .foods-wrapper .food-list .food-item .content .product {
  height: 15px;
  margin-bottom: 6px;
}
/* ？？？ */
.goods .foods-wrapper .food-list .food-item .content .price {
  font-size: 0;
}
/* 月售***  赞*** 大盒子--售价 */
.goods .foods-wrapper .food-list .food-item .content .price .text {
  font-size: 14px;
  color: #fb4e44;
}
/* 月售***  赞*** 大盒子--单位 */
.goods .foods-wrapper .food-list .food-item .content .price .unit {
  font-size: 12px;
  color: #bfbfbf;
}

/* 当前选中就会产生此样式*/
.goods .menu-wrapper .menu-item.current {
  background: white;
  font-weight: bold;
  margin-top: -1px;
}
.goods .menu-wrapper .menu-item:first-child.current {
  margin-top: 1px;
}


.goods .foods-wrapper .food-list .food-item .cartcontrol-wrapper {
  right: 0;
  bottom: 0;
  position: absolute;
}

.goods .menu-wrapper .menu-item .num {
  position: absolute;
  right: 5px;
  top: 5px;
  width: 13px;
  height: 13px;
  border-radius: 50%;
  color: white;
  background: red;
  text-align: center;
  font-size: 2.5px;
  line-height: 13px;
}
</style>
