<template>
  <div class="scre">
    <div
      class="scre__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 + 16) * Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <card-flip
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index: index, value: card }"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>

<script>
import CardFlip from "./Card.vue";

export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  components: {
    CardFlip,
  },
  data() {
    return {
      rules: [],
    };
  },
  methods: {
    checkRule(card) {
      //nếu mảng đã có 2 phần tử rồi thì không xử lý thêm phần tử nữa
      if (this.rules.length === 2) return false;

      //thêm phần tử khi mảng compare chưa có đủ 2 phần tử để so sánh
      this.rules.push(card);

      //khi đã đủ 2 phần tử
      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        //chọn đúng -> hủy event click,lưu lại giá trị chọn đúng

        //add class 'disabled' to component card
        this.$refs["card-" + this.rules[0].index][0].onDisableMode();
        this.$refs["card-" + this.rules[1].index][0].onDisableMode();

        //xóa mảng lưu giá trị
        this.rules = [];

        //nếu chọn đúng hết thì kết thúc trò chơi
        //mỗi lần lật đúng thì sẽ thêm class disabled vào -> nếu tất cả đều có class disable thì => đã lật đúng hết
        const disableElemnts = document.querySelectorAll(
          ".scre .card.disabled"
        );
        console.log(disableElemnts);

        //lần đầu chọn đúng NodeList length vẫn là 0 (bị trễ 2 phần tử được chọn) => +2 để bù lại cho đủ
        if (
          disableElemnts &&
          disableElemnts.length + 2 === this.cardsContext.length
        ) {
          //hoàn thành game
          setTimeout(() => {
            this.$emit("onFinish");
          }, 900);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        //chọn sai -> đóng các thẻ đã chọn lại, xóa phần tử trong mảng rule

        //đặt độ trễ xóa thẻ vì xóa ngay lập tức thì không thấy được
        setTimeout(() => {
          //đóng cá thẻ đã chọn sai, ref có thể truy cập được phương thức thuộc tính... của component child
          this.$refs["card-" + this.rules[0].index][0].onFilpBackCart();
          this.$refs["card-" + this.rules[1].index][0].onFilpBackCart();

          //xóa mảng vừa chọn sai
          this.rules = [];
        }, 600);
      } else return false;
    },
  },
};
</script>

<style lang="css" scoped>
.scre {
  width: 100%;
  height: 150vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}

.scre__inner {
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
