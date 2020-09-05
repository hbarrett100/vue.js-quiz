<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        <!-- question is passed from parent component in app.vue -->
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4" />

      <b-list-group>
        <!-- each item in for loop needs a unique identifier key. Can use index -->
        <b-list-group-item
            v-for="(answer, index) in shuffledAnswers" 
            :key="index"
            @click="selectAnswer(index)"
            :class="answerClass(index)"
        >
            {{answer}}
        </b-list-group-item>
      </b-list-group>

      <b-button 
      variant="primary" 
      @click="submitAnswer"
      :disabled="selectedIndex === null || answered"
      >
          Submit</b-button>
      <b-button @click="next" variant="success" href="#">Next Question</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'
export default {
  // any variables (data or methods)
  // passed from another component to this one need to be referenced in props
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },

    // define all variables
  data() {
      return {
          selectedIndex: null,
          correctIndex: null,
          shuffledAnswers: [],
          answered: false
      }
  },

  computed: {
    answers() {
      //make copy of array
      // combine correct and incorrect answers
      // 'this' needed as it is a prop (same for data variable)
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },

    // watch for a change in currentQuestion
  watch: {
      // using immediate: true means that this function gets run not only when current
      // question is updated but also when current question is first passed as props and every
      // subsequent time it updates
      currentQuestion: {
          immediate: true,
          handler() {
            this.selectedIndex = null
            this.answered = false
            this.shuffleAnswers()
          } 
      }
  },

    methods: {
        selectAnswer(index) {
            this.selectedIndex = index
        },

        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        submitAnswer(){
            let isCorrect = false

            if (this.selectedIndex === this.correctIndex) {
                isCorrect = true
            }
            this.answered = true

            this.increment(isCorrect)
        },
        answerClass(index) {
            let answerClass = ''

            if (!this.answered && this.selectedIndex === index) {
                answerClass = 'selected'
            } else if (this.answered && this.correctIndex === index) {
                answerClass = 'correct'
            } else if (this.answered && 
                this.selectedIndex === index && 
                this.correctIndex !== index) {
                answerClass = 'incorrect'
            }
            return answerClass
        }
    }
};
</script>


<style scoped>
.list-group {
    margin-bottom: 15px;
}

.list-group-item:hover {
    background-color: #EEE;
    cursor: pointer;
}

.btn {
    margin: 0 5px;
}

.selected {
    background-color: lightblue;
}

.correct {
    background-color: lightgreen;
}

.incorrect {
    background-color: red;
}
</style>