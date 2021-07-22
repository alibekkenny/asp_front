<template>
  <div class="w-full flex justify-center">
    <div class="w-1/2 border-gray-600 border rounded p-10 mt-5">
      <span class="flex mb-2">№ {{ this.index + 1 }}</span>
      <span class="flex">Вопрос: {{ this.question.content }}</span>
      <span class="flex">Варианты:</span>
      <div class="mt-2 ml-2 mb-4">
        <div>
          <label class="inline-flex items-center">
            <input
              id="radio1"
              type="radio"
              class="form-radio"
              name="radio"
              value="1"
              checked
            />
            <span class="ml-2">1</span>
          </label>
        </div>
        <div>
          <label class="inline-flex items-center">
            <input type="radio" class="form-radio" name="radio" value="2" />
            <span class="ml-2">2</span>
          </label>
        </div>
        <div>
          <label class="inline-flex items-center">
            <input type="radio" class="form-radio" name="radio" value="3" />
            <span class="ml-2">3</span>
          </label>
        </div>
        <div>
          <label class="inline-flex items-center">
            <input type="radio" class="form-radio" name="radio" value="4" />
            <span class="ml-2">4</span>
          </label>
        </div>
        <div>
          <label class="inline-flex items-center">
            <input type="radio" class="form-radio" name="radio" value="5" />
            <span class="ml-2">5</span>
          </label>
        </div>
      </div>
      <button
        v-if="this.index != 0"
        class="bg-blue-500 hover:bg-blue-700 text-white py-2 px-4 rounded"
        @click="goPrev()"
      >
        Предыдущий
      </button>
      <button
        v-if="this.index != this.maxIndex"
        class="
          bg-blue-500
          hover:bg-blue-700
          text-white
          py-2
          px-4
          rounded
          float-right
        "
        @click="goNext()"
      >
        Следующий
      </button>
      <button
        class="float-right"
        v-if="this.index == this.maxIndex"
        @click="saveAnswer()"
      >
        <Nuxt-Link
          class="bg-red-500 hover:bg-red-700 text-white rounded flex py-2 px-4"
          to="/diagram"
        >
          Завершить
        </Nuxt-Link>
      </button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { mapMutations } from "vuex";
export default {
  data() {
    return {
      question: {},
      index: 0,
      maxIndex: 0,
    };
  },
  async mounted() {
    const questions = (await axios.get("https://localhost:44302/questions"))
      .data;
    this.initQuestions(questions);
    this.index = this.$store.state.questions.index;
    this.question = this.$store.state.questions.questions[this.index];
    this.maxIndex = questions.length - 1;
  },
  methods: {
    ...mapMutations({
      initQuestions: "questions/init",
      next: "questions/nextIndex",
      prev: "questions/prevIndex",
    }),
    goNext() {
      this.next();
      this.saveAnswer();
    },
    goPrev() {
      this.prev();
      this.saveAnswer();
    },
    saveAnswer() {
      let quiz = 0;
      const elements = document.getElementsByClassName("form-radio");
      elements.forEach((x) => {
        if (x.checked == true) {
          quiz = Number(x.value);
        }
      });
      const answer = {
        quiz: quiz,
        userID: "08d94ac0-57f2-4590-8776-6412d13f95ae",
        questionId: this.question.id,
      };
      const { data, status } = axios.post(
        "https://localhost:44302/answers",
        answer
      );
      document.getElementById("radio1").checked = true;
      console.log(data);
      this.index = this.$store.state.questions.index;
      this.question = this.$store.state.questions.questions[this.index];
    },
  },
};
</script>
