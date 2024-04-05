<template>
  <div class="numbers-container">
    <div
        v-for="(item, index) in numbers"
        :key="index"
        :class="{
          'clicked': item.clicked,
          'defect': displayAnswer && answers.includes(item.number) && !item.clicked,
          'unnecessary': displayAnswer && !answers.includes(item.number) && item.clicked
        }"
        @click="toggleClick(index)"
        class="number-item"
    >
      {{ item.number }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    numbers: Array,
    displayAnswer: Boolean,
    answers: Array
  },
  methods: {
    toggleClick(index) {
      if (!this.displayAnswer) {
        this.$emit('update:numbers', this.numbers.map((item, i) => {
          if (i === index) {
            return {...item, clicked: !item.clicked};
          }
          return item;
        }));
      }
    },
  },
};
</script>

<style scoped>
.numbers-container {
  display: flex; /* Flexbox コンテナを有効化 */
  flex-wrap: wrap; /* 子要素がコンテナの幅を超えた場合に折り返し */
  justify-content: center; /* 水平方向の中央に子要素を配置 */
  border: 1px solid black;
  padding: 0.25rem 2rem;
  width: fit-content;
}

.number-item {
  margin: 5px;
  padding: 0 10px;
  cursor: pointer;
  user-select: none;
  font-size: 32px;
  width: 1.2rem;
  text-align: center;
}

.clicked {
  position: relative;
}

.clicked:before {
  content: "";
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(-38deg, transparent 48%, black 48%, black 54%, transparent 54%);
}

.defect {
  background-color: lightgreen;
}

.unnecessary {
  background-color: #fa5252;
}

</style>
