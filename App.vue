<script>
import testFile from "./test.json";

export default {
  data() {
    return {
      question: 0,
      answers: [],
    };
  },
  methods: {
    start() {
      this.question = 1;
    },
    check(event) {
      const index = event.target.value;
      if (this.answers[index] == undefined) {
        this.answers[index] = 0;
      }
      if (event.target.checked) {
        this.answers[index] += 1;
      } else {
        this.answers[index] -= 1;
      }
    },
    answer() {
      this.question += 1;
    },
    percent(index) {
      if (!this.answers[index]) {
        return 0;
      }
      return Math.floor((this.answers[index] * 100) / this.test.Results.length);
    },
    results() {
      return this.test.Results.sort((a, b) => {
        const ar = parseInt(this.answers[a.Result] || 0, 10);
        const br = parseInt(this.answers[b.Result] || 0, 10);
        return br - ar;
      });
    },
  },
  computed: {
    test() {
      const t = testFile;
      t.Questions.sort(() => Math.random() - 0.5);
      for (let i = 0; i < t.Questions.length; i++) {
        t.Questions[i].Answers.sort(() => Math.random() - 0.5);
      }
      return t;
    },
    opened() {
      return this.question <= 0;
    },
    processed() {
      return this.question > 0 && !this.done;
    },
    done() {
      return this.question >= this.test.Questions.length;
    },
    ready() {
      let sum = 0;
      this.answers.forEach((a) => {
        sum += a;
      });
      return sum > 0 && sum % 3 == 0 && sum / 3 == this.question;
    },
  },
};
</script>

<template lang="pug">
header
  h1 Ведущие мотивы трудовой деятельности
  p Арипова М. С.

main(v-if="opened")
  button(@click="start") Начать тестирование

main(v-if="processed")
  form(v-for="q, qi in test.Questions")
    article(v-if="qi == (question - 1)")
      h2 {{ q.Text }}
      label(v-for="a in q.Answers")
        input(type="checkbox" name="a", :value="a.Result", @change="check")
        p {{ a.Text }}
  p(v-if="!ready") Выберите ровно 3 варианта ответа.
  button(@click="answer" v-if="ready") Следующий вопрос

main(v-if="done")
  h2 Результаты тестирования
  article(v-for="a, ai in results()")
    h3 {{ percent(a.Result) }}% – {{ a.Title }}
    p {{ a.Text }}
</template>

<style lang="stylus">
@import './base.css'

#app
  padding 50px
  margin 0
  font-weight normal

header
  display block
  padding-bottom 20px
  margin-bottom 20px

  border-bottom 1px solid grey

  h1
    display block
    padding 0
    margin 0
    font-size 2em

  p
    display block
    padding 0
    margin 0
    font-style italic
    font-size 1.2em

main
  display block

  h2, h3
    display block
    margin 0
    padding 0

    margin-bottom 10px
    padding-bottom 10px
    border-bottom 1px dotted grey

  article
    padding-bottom 15px
    margin-bottom 15px

  button
    padding 10px 32px

    background-color white
    border 1px solid grey
    color grey

    font-size 1.5em
    cursor pointer

  label
    display block

    margin 15px 0
    padding 5px
    padding-left 30px

    position relative

    input
      display block

      width 20px
      height @width

      position absolute
      top 1em
      margin-top -2px
      left 0

      border 2px solid grey
</style>
