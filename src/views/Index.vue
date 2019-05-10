<template>
  <div id="index">
    <!-- 轮播图 -->
    <cube-slide ref="slide" :data="items" @change="changePage">
      <cube-slide-item
        v-for="(item, index) in items"
        :key="index"
        @click.native="clickHandler(item, index)"
      >
        <img class="banner" :src="item.image">
      </cube-slide-item>
    </cube-slide>
    <!-- 滚动分类 -->
    <cube-slide ref="slidelists" :auto-play="false" :data="lists">
      <cube-slide-item v-for="(list, index) in lists" :key="index">
        <ul class="listul">
          <li class="listli" v-for="(item,index) in list" :key="index">
            <img :src="item.image" alt>
            <p>{{item.label}}</p>
          </li>
        </ul>
      </cube-slide-item>
    </cube-slide>
    <!--Recommend goods area-->
    <div class="recommend-area">
      <div class="recommend-title">商品推荐</div>
      <div class="recommend-body">
        <swiper :options="swiperOption">
          <swiper-slide v-for="(item,index) in recommendGoods " :key="index">
            <div class="recommend-item">
              <img :src="item.image" width="80%">
              <div>{{item.goodsName}}</div>
              <div>￥{{item.price | moneyFilter}}(￥{{item.mallPrice | moneyFilter}})</div>
            </div>
          </swiper-slide>
        </swiper>
      </div>
    </div>
    <floor-component :floorData="floor1" :floorTitle="floorName.floor1"></floor-component>
    <floor-component :floorData="floor2" :floorTitle="floorName.floor2"></floor-component>
    <floor-component :floorData="floor3" :floorTitle="floorName.floor3"></floor-component>
  </div>
</template>

<script>
import "swiper/dist/css/swiper.css";
import { swiper, swiperSlide } from "vue-awesome-swiper";
import floorComponent from "../components/floorComponent";
import { toMoney } from "../filter/moneyFilter.js";
export default {
  data() {
    return {
      swiperOption: {
        slidesPerView: 3
      },
      items: [], //轮播图数组
      lists: [], //滚动分类数组
      recommendGoods: [],
      floor1: [],
      floor2: [],
      floor3: [],
      floorName: {}
    };
  },
  filters: {
    moneyFilter(money) {
      return toMoney(money);
    }
  },
  components: { swiper, swiperSlide, floorComponent },
  methods: {
    changePage(current) {
      //   console.log('当前轮播图序号为:' + current)
    },
    clickHandler(item, index) {
      console.log(item, index);
    }
  },
  async created() {
    try {
      //获取轮播图数据
      const items = await this.$http.get("/api/banner");
      this.items = items.data;
      //获取滚动分类数据
      const lists = await this.$http.get("/api/rollinglist");
      this.lists = lists.data;
      //
      const shoplists = await this.$http.get(
        "https://www.easy-mock.com/mock/5cb032271724331619d828ad/vue-shop/index"
      );
      this.recommendGoods = shoplists.data.recommend;
      this.floor1 = shoplists.data.floor1;
      this.floor2 = shoplists.data.floor2;
      this.floor3 = shoplists.data.floor3;
      this.floorName = shoplists.data.floorName;
    } catch (err) {
      console.log(err);
    }
  }
};
</script>

<style lang="stylus" scoped>
#index {
  .banner {
    display: block;
    width: 100%;
    height: 175px;
  }

  .listul {
    display: flex;
    flex-wrap: wrap;
  }

  .listli {
    width: 20%;
    justify-content: center;

    img {
      width: 35px;
      height: 35px;
      border-radius: 50%;
      padding: 5px 0;
    }

    p {
      font-size: 14px;
      padding-bottom: 10px;
    }
  }

  .recommend-area {
    background-color: #fff;
    margin-top: 0.3rem;

    .recommend-title {
      border-bottom: 1px solid #eee;
      font-size: 14px;
      padding: 0.2rem;
      color: red;
    }

    .recommend-body {
      border-bottom: 1px solid #eee;

      .recommend-item {
        width: 99%;
        border-right: 1px solid #eee;
        font-size: 12px;
        text-align: center;
      }
    }
  }
}
</style>

