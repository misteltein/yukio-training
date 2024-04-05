<template>
  <div class="numbers-page">
    <div v-if="status==='explanation'">
      <h1>問題用紙1</h1>
      <div>
        <p>これから、たくさん数字が書かれた表が出ますので、私が指示した数字に斜線を引いてもらいます。</p>
        <p>例えば、「１と４」に斜線を引いてくださいといったときは、</p>
        <div class="rows">
          <img src="@/assets/arrow.png" alt="arrow" class="arrow-img"/>
          <NumbersRow
              v-for="(row, index) in sampleRows"
              :key="index"
              :numbers="row"
          />
        </div>
        <p class="without-indent">と例示のように順番に、見つけただけ斜線を引いてください。</p>
      </div>
      <div class="next">
        <button type="button" @click="status='start'">問題へ</button>
      </div>
    </div>
    <div v-if="status==='start'">
      <div class="window-center">
        <p class="without-indent">
          「{{ answers[0] }}と{{ answers[1] }}」に斜線を引いてください。
        </p>
        <div class="next">
          <button type="button" @click="status='playing'">回答を始める</button>
        </div>
      </div>
    </div>
    <div v-if="status==='playing'">
      <h1 v-if="success===undefined">回答用紙1</h1>
      <h1 v-if="success===true">正解です</h1>
      <h1 v-if="success===false">不正解です</h1>
      <div class="rows">
        <img src="@/assets/arrow.png" alt="arrow" class="arrow-img"/>
        <NumbersRow
            v-for="(row, index) in rows"
            :key="index"
            :numbers="row"
            :displayAnswer="success!==undefined"
            :answers="answers"
            @update:numbers="(newNumbers) => updateNumbers(index, newNumbers)"
        />
      </div>
      <button v-if="success===undefined" @click="confirmAnswers">回答を確定する</button>
      <button v-else @click="status='explanation'">もう一度</button>
    </div>
  </div>
</template>

<script>
import NumbersRow from '../parts/NumbersRow.vue';

export default {
  components: {
    NumbersRow,
  },
  data() {
    return {
      status: 'explanation', // explanation, start, playing, result
      success: undefined,
      answers: [],
      rows: [],
      sampleRows: [],
    };
  },
  watch: {
    status(newStatus) {
      // 一番上までスクロール
      window.scrollTo(0, 0);
      if(newStatus==='explanation') {
        this.success = undefined;
        this.initializeAnswers();
        this.initializeRows();
      }
    },
  },
  created() {
    this.setSampleRows();
    this.initializeAnswers();
    this.initializeRows();
  },
  methods: {
    updateNumbers(rowIndex, newNumbers) {
      this.rows.splice(rowIndex, 1, newNumbers);
    },
    initializeAnswers() {
      const r1 = Math.floor(Math.random() * 9 + 1);
      let r2 = r1;
      while (r2 === r1) {
        r2 = Math.floor(Math.random() * 9 + 1);
      }
      this.answers = [r1, r2];
    },
    initializeRows() {
      this.rows = [];
      for (let i = 0; i < 10; i++) {
        this.rows.push(this.createRandomNumbers());
      }
    },
    setSampleRows() {
      this.sampleRows.push([
        {number: 4, clicked: true},
        {number: 3, clicked: false},
        {number: 1, clicked: true},
        {number: 4, clicked: true},
        {number: 6, clicked: false},
        {number: 2, clicked: false},
        {number: 4, clicked: true},
        {number: 7, clicked: false},
        {number: 3, clicked: false},
        {number: 9, clicked: false},
      ]);
      this.sampleRows.push([
        {number: 8, clicked: false},
        {number: 6, clicked: false},
        {number: 3, clicked: false},
        {number: 1, clicked: true},
        {number: 8, clicked: false},
        {number: 9, clicked: false},
        {number: 5, clicked: false},
        {number: 6, clicked: false},
        {number: 4, clicked: true},
        {number: 3, clicked: false},
      ]);
    },
    createRandomNumbers() {
      // 1から9までのランダムな数を10個生成
      return Array.from({length: 10}, () => ({
        number: Math.floor(Math.random() * 9 + 1),
        clicked: false,
      }));
    },
    confirmAnswers() {
      let correct = true;

      // 全てのrowsをチェック
      for (const row of this.rows) {
        for (const item of row) {
          const isInAnswers = this.answers.includes(item.number);
          // 答えに含まれる数字がクリックされていない、または答えに含まれない数字がクリックされている場合、不正解
          if ((isInAnswers && !item.clicked) || (!isInAnswers && item.clicked)) {
            correct = false;
            break;
          }
        }
        if (!correct) break;
      }

      this.success = correct;
      window.scrollTo(0, 0);
    },
  },
};
</script>
<style scoped>
.numbers-page {
  text-align: center;
  width: 100vw;
  margin: 50px 0;
}

h1 {
  letter-spacing: 1.5rem;
  border: 2px solid black;
  width: fit-content;
  margin: 0 auto;
  padding: 5px 20px;
  margin-bottom: 60px;
}

p {
  width: 15rem;
  margin: 0 auto;
  text-align: left;
  text-indent: 1em;
}

.without-indent {
  text-indent: 0;
}

.rows {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100vw;
  margin: 30px 0;
}

.next {
  margin-top: 60px;
}

.arrow-img {
  width: 100px;
  margin: 0 auto 10px calc(50% - 9.6rem);
}

.window-center {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
