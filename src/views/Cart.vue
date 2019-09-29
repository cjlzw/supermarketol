<template>
  <div class="cart">
    <van-checkbox-group class="card-goods" v-model="checkedGoods">
      <van-swipe-cell v-for="(item,index) in goods" :key="index">
        <van-checkbox class="card-goods__item" :name="item.id" :checked="true" v-model="checked">
          <van-card
            :title="item.title"
            :desc="item.desc"
            :num="item.num"
            :price="formatPrice(item.price)"
            :thumb="item.thumb"
          />
        </van-checkbox>
        <template slot="right">
          <van-button square type="danger" text="删除" @click="remove(index)" />
        </template>
      </van-swipe-cell>
    </van-checkbox-group>
    <van-submit-bar
      :price="totalPrice"
      :disabled="!checkedGoods.length"
      :button-text="submitBarText"
      @submit="onSubmit"
    >
      <van-checkbox v-model="checked" @click="allcheck">全选</van-checkbox>
    </van-submit-bar>
  </div>
</template>

<script>
import {
  SwipeCell,
  Checkbox,
  CheckboxGroup,
  Card,
  SubmitBar,
  Toast,
  Button,
  Dialog
} from "vant";

import { mapState, mapMutations } from "vuex";

export default {
  components: {
    [Card.name]: Card,
    [SwipeCell.name]: SwipeCell,
    [Checkbox.name]: Checkbox,
    [SubmitBar.name]: SubmitBar,
    [CheckboxGroup.name]: CheckboxGroup,
    [Button.name]: Button,
    [Dialog.name]: Dialog
  },
  data() {
    return {
      checked: false,
      checkedGoods: [],
      panduan: true,
      goods: ""
    };
  },
  created() {
    this.goods = this.$store.state.selectgoods;
  },
  computed: {
    ...mapState(["selectgoods"]),
    submitBarText() {
      const count = this.checkedGoods.length;
      return "结算" + (count ? `(${count})` : "");
    },
    totalPrice() {
      let total = this.goods.reduce(
        (total, item) =>
          total +
          (this.checkedGoods.indexOf(item.id) !== -1
            ? item.price * item.num
            : 0),
        0
      );
      return total;
    }
  },
  watch: {
    checkedGoods: {
      handler: function() {
        if (this.checkedGoods.length == this.goods.length) {
          this.checked = true;
        } else {
          this.checked = false;
        }
      }
    }
  },
  methods: {
    ...mapMutations(["toPay"]),
    remove(index) {
      // console.log(index);
      // e.cancelBubble = true;
      Dialog.confirm({
        title: "确定删除?"
      })
        .then(() => {
          console.log(index);
          this.goods.splice(index, 1);
        })
        .catch(() => {
          // on cancel
        });
    },
    formatPrice(price) {
      return (price / 100).toFixed(2);
    },
    allcheck() {
      console.log("全选");
      if (this.checked) {
        this.checkedGoods = [];
      } else {
        this.checkedGoods = [];
        this.goods.forEach(item => {
          this.checkedGoods.push(item.id);
        });
      }
      console.log(this.checked);
    },
    //结算
    onSubmit() {
      Toast("点击结算");
      let settlement = this.goods.filter(item => {
        return this.checkedGoods.indexOf(item.id) !== -1;
      });
      console.log(settlement)
      let totalprice = settlement.reduce((total,item)=>{
        return total + item.price*item.num
      },0)
      console.log(totalprice);
      this.toPay({settlement,totalprice});
      this.$router.push({
        path: "/createorder"
      });
    }
  }
};
</script>

<style lang="scss">
.cart {
  .card-goods {
    margin: 10px 0;
    &__item {
      position: relative;
      //   background: #fafafa;
      //   background: palevioletred;
      .van-checkbox__label {
        margin-left: 0;
        flex: 1;
      }
      .van-card {
        padding: 5px 15px 5px 0;
      }
      .van-card__price {
        color: #f44;
      }
      .van-card__content {
        font-size: 14px;
      }
      .van-card__title {
        line-height: 1.5rem;
      }
    }
  }
  .van-checkbox__icon {
    -webkit-box-flex: 0;
    -webkit-flex: none;
    flex: none;
    height: 1em;
    font-size: 1.25rem;
    line-height: 1em;
    margin: 10px;

    // background: #f44;
  }
  .van-submit-bar {
    position: fixed;
    bottom: 60px;
    left: 0;
    z-index: 100;
    width: 100%;
    background-color: #fff;
    -webkit-user-select: none;
    user-select: none;
  }
  .van-button--square {
    height: 100%;
  }
}
</style>