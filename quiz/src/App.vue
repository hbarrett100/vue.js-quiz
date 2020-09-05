<template>
  <div id="app">
    <Header 
      :numCorrect="numCorrect"
      :numTotal="numTotal"
    />

    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset="3">
            <!-- only render question box when data becomes available -->
          <QuestionBox 
          v-if="questions.length"
          :currentQuestion="questions[index]"
          :next="next"
          :increment="increment"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import QuestionBox from "./components/QuestionBox.vue";

export default {
  name: "App",
  components: {
    Header,
    QuestionBox
  }, 

  // create data object and return an object
  data() {
    return {
      //initialise empty array, will be filled with data from api
      questions: [],
      index: 0,
      numCorrect: 0, 
      numTotal: 0
    }
  },

  methods: {
    next() {
      this.index++
    },
    // keep track of total no. of submissions and no. of correct submissions
    increment(isCorrect) {
      if (isCorrect) {
        this.numCorrect++
      }
      this.numTotal++
    }
  },

  mounted: function() {
    // fetch the questions API
    fetch('https://opentdb.com/api.php?amount=10&category=27&type=multiple', {
      method: 'get'
    })
      .then((response) => {
        return response.json()
      })
      .then((jsonData) => {
        this.questions = jsonData.results
      })
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
