<template>
  <div class="app-container">
    <!-- Header头部 -->
    <Header></Header>
    <!-- Goods商品部分 -->
    <Goods
      v-for="item in list"
      :key="item.id"
      :id="item.id"
      :pic="item.goods_img"
      :name="item.goods_name"
      :price="item.goods_price"
      :state="item.goods_state"
      :count="item.goods_count"
      @changeState="getNewState"
    ></Goods>
    <!-- Footer部分 -->
    <Footer
      :isFall="fullState"
      :numPrice="numPrice"
      :numCount="numCount"
      @setFull="getSetFull"
    ></Footer>
  </div>
</template>

<script>
import axios from "axios";
import bus from "@/eventBus.js";
import Header from "@/components/Header/Header.vue";
import Goods from "@/components/Goods/Goods.vue";
import Footer from "@/components/Footer/Footer.vue";
export default {
  data() {
    return {
      list: [],
    };
  },
  components: {
    Header,
    Goods,
    Footer,
  },
  methods: {
    async initCartList() {
      const { data: res } = await axios.get("https://www.escook.cn/api/cart");
      this.list = res.list;
      console.log(res.list);
    },
    getNewState(val) {
      this.list.some((item) => {
        if (val.id == item.id) {
          item.goods_state = val.value;
        }
      });
    },
    getSetFull(val) {
      this.list.forEach((item) => (item.goods_state = val));
    },
  },
  created() {
    this.initCartList();
    bus.$on("share", (val) => {
      this.list.some((item) => {
        if (item.id == val.id) {
          item.goods_count = val.value;
        }
      });
    });
  },
  computed: {
    fullState() {
      return this.list.every((item) => item.goods_state);
    },
    numPrice() {
      return this.list
        .filter((item) => item.goods_state)
        .reduce(
          (total, item) => (total += item.goods_price * item.goods_count),
          0
        );
    },
    numCount() {
      return this.list
        .filter((item) => item.goods_state)
        .reduce((total, item) => (total += item.goods_count), 0);
    },
  },
};
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
