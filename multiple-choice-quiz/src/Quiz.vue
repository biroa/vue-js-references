<script setup>
import {ref, computed, onMounted, reactive} from "vue";

const answer = ref('');
const message = ref({});
const isDisabled = ref(false);
const currentQuestion = ref(0);
const nextButtonDisabled = ref();
const messageType = computed({
  get(){
    return message.value
  },
  set(messageNumbers){
    switch (messageNumbers){
      case 0:
            message.value = {'text': "Not valid answer! Or no answer at all!", 'color': "text-red-600"};
            break;
      case 1:
            message.value = {'text': "Correct answer!", 'color': "text-green-600"};
            break;
      case 2:
            message.value = {'text': "Select an option!", 'color': "text-orange-600"};
            break;
      default:
            message.value = {'text': "Select an option!", 'color': "text-orange-600"};
    }
  },
})

const initMessageType = (val)=> {
  if(val === 2){
    nextButtonDisabled.value = true;
  }else{
    nextButtonDisabled.value = false;
  }
  console.log(val,nextButtonDisabled.value)
  messageType.value = val;
}


/**
 * @description Go to the next Slide
 */
const nextSlide = () => {
  currentQuestion.value = currentQuestion.value+1;
  enableDisableSelection();
  resetAnswer();
  initMessageType(2);
}

/**
 * @description Detect when we need to show the quiz statistic
 */
const lastQuestion = () => {
  return questions.length > currentQuestion.value
}

const questions = reactive([
  {
    question: "What is the capital of France?",
    options: ["London", "Berlin", "Paris", "Rome"],
    answer: "Paris",
    answerIsCorrect: null,
  },
  {
    question: "Which planet is closest to the sun?",
    options: ["Earth", "Mars", "Venus", "Mercury"],
    answer: "Mercury",
    answerIsCorrect: null,
  },
  {
    question: "Which planet is closest to the sun?",
    options: ["Earth", "Mars", "Venus", "Mercury"],
    answer: "Mercury",
    answerIsCorrect: null,
  },
  // Add more questions as needed
]);

/**
 * @description Enable / Disable  radio checkboxes options
 */
const enableDisableSelection = ()=>{
  isDisabled.value= !isDisabled.value;
  theAnswerIsValid();
}
/**
 * @description Sum the score of the quiz
 */
const scoreSum = computed(()=>{
  let list =  questions.map( o => o.answerIsCorrect )
  console.log(list);
  let sum = list.reduce((accumulator, current) => accumulator + current, 0);
  console.log('here',list, sum);
  return sum;
});

/**
 * @description Validate the player's answer
 * @type {function(): boolean|*}
 */
const theAnswerIsValid = ()=>{
  let questionsSrc = {...questions};
  if(questions.length > currentQuestion.value){
    if(questions[currentQuestion.value]?.answer.toLowerCase() === answer.value.toLowerCase()){
      questionsSrc[currentQuestion.value].answerIsCorrect = 1;
      Object.assign(questions,questionsSrc)
      initMessageType(1);
    }else{
      questionsSrc[currentQuestion.value].answerIsCorrect = 0;
      Object.assign(questions,questionsSrc)
      initMessageType(0);
    }
  }
}


/**
 * @description Reset the answer model
 */
const resetAnswer = ()=>{
  answer.value = "";
}

onMounted(()=> {
  initMessageType(2);
});

</script>

<template>
  <div class="p-2 my-2">
    <p class="text-orange-600">{{messageNumbers}}</p>
    <div  v-show="lastQuestion()">
      <p class="font-mono text-white">
        {{questions[currentQuestion]?.question}}
      </p>

      <hr>
      <div class="m-2">
        <div class="" v-for="option in questions[currentQuestion]?.options">
          <input :disabled="isDisabled" @change="enableDisableSelection()" type="radio" v-model="answer" :value="option" :name="option" :id="option">
          <label class="px-2 text-slate-50" :for="option">{{option}}</label>
        </div>
      </div>
      <span class="px-2" :class="messageType.color">
        {{messageType.text}}
      </span>
      <button class="bg-gray-400 m-2 py-1 px-4 float-right"
              :disabled="nextButtonDisabled"
              @click.prevent="nextSlide()" type="button" name="Next">
        Next
      </button>

    </div>
    <div  v-show="!lastQuestion()">
      <h2 class="font-mono text-white text-center">The Game is Over</h2>
      <p class="px-2 text-orange-50">Your final score:
        <span class="px-2 text-orange-50">{{scoreSum}}</span>
        <span class="px-2 text-orange-50">/</span>
        <span class="px-2 text-orange-50">{{questions.length}}</span>
      </p>
    </div>
  </div>
</template>
