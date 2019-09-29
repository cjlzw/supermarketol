<template>
  <div class="tabber" v-if="show">
    <van-tabbar route active-color="steelblue" inactive-color="#000">
      <van-tabbar-item replace to="/home" icon="home-o">首页</van-tabbar-item>
      <van-tabbar-item replace to="/category" icon="apps-o">分类</van-tabbar-item>
      <van-tabbar-item replace to="/cart" icon="cart-o">购物车</van-tabbar-item>
      <van-tabbar-item replace to="/user" icon="contact">我的</van-tabbar-item>
    </van-tabbar>
  </div>
</template>

<script>
import { Tabbar, TabbarItem } from "vant";
export default {
  name: "tabber",
  components: {
    
    [Tabbar.name]: Tabbar,
    [TabbarItem.name]: TabbarItem
  },
  data() {
    return {
      show: true
    };
  },
  watch: {
    $route(to, from) {
      console.log(to.path);
      if (to.path === "/product" || to.path === "/comments" || to.path === "/createorder") {
        this.show = false;
      } else {
        this.show = true;
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
