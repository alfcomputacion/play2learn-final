<template>
  <div id="main-container">
    
    <div class="header-space"></div>
    <div v-if="screen === 'config'" id="config-container">
    <h1>Mathificent</h1>
    <SelectInput 
        :currentValue="operation"
        v-model="operation" 
        label="Operations" 
        id="operation"  
        :options="operations"/>
    <select-input 
        :currentValue="maxNumber"
        v-model="maxNumber"
        label="Max Number" id="maxNumber" 
        :options="numbers" />
    <PlayButton 
    :btnclass="btnClass" 
    :label="btnLabel" 
    @le-button-click="play"  />
    </div>
    <div v-else-if="screen === 'play'" id="game-container" class="text-center">
      <transition name="slide">
      <template v-if="timeLeft === 0">
        <div>
            <h2>Time's Up!</h2>
            <strong class="big">You Answered</strong>
            <div id="score-id" class="huge">{{score}}</div>
            <strong class="big">Questions Correctly</strong>
            <button class="btn btn-primary form-control m-1"
            @click="restart()">
            Play Again with Same Settings
            </button>
            <button class="btn btn-secondary form-control m-1"
            @click="config()">
            Change Settings
            </button>
        </div>

      </template>
      </transition>
      <transition name="slide-right">

          <template v-if="timeLeft > 0">
            <div>
                <div class="row border-bottom" id="scoreboard">
                    <div class="col px-3 text-left">
                        <Score :score="score" />
                    </div>
                    <div class="col px-3 text-right">
                        <Timer :timeLeft="timeLeft"/>
                    </div>
                </div>
                <div class="row text-secondary my-2" id="equation">
                      <Equation :question="question"
                        :answer="input"
                        :answered="answered" />
                </div>
                <div class="row" id="buttons">
                    <div class="col">
                    <button class="btn btn-primary number-button" 
                            @click="setInput(button)"
                            v-for="button in buttons" 
                            :key="button">{{button}}
                            </button>

                    <button @click="clearAnswer" id="clear-button" class="btn btn-primary" >Clear</button>
                    </div>
                </div>
            </div>
          </template>
      </transition>
       <div  id="game-points" class="gameon">1 <strong>++</strong></div>
    </div>

  </div>
</template>

<script>
import TheHeader from './TheHeader.vue'
import SelectInput from './SelectInput.vue'
import PlayButton from './PlayButton.vue';
import Score from './Score.vue';
import Timer from './Timer.vue';
import Equation from './Equation.vue';
import {randInt} from '../helpers/helpers'


export default {
  name: 'TheMathificent',
  data: function(){
    return{
      operations: [
        ['Addition', '+'],
        ['Subtraction', '-'],
        ['Multiplication', 'x'],
        ['Division', '/'],
      ],
      operation: 'x',
      maxNumber: '10',
      buttons: [1,2,3,4,5,6,7,8,9,0],
      screen: 'config',
      input: '',
      operands: {num1: '1', num2: '1'},
        answered: false,
        score: 0,
        gameLength: 60,
        timeLeft: 0,
        btnLabel: 'Play!',
        btnClass: 'form-control btn btn-primary'
        
    }

  },
  computed: {
    numbers: function(){
         const numbers = [];
         for(let number=2; number <= 100; number++){
          numbers.push([number,number]);
         }
         return numbers;
    },
  
    
    question: function(){
        const num1 = this.operands.num1;
        const num2 = this.operands.num2;
        const equation = `${num1} ${this.operation} ${num2}`;
        return equation;
    }
 
  },
  mounted(){document.addEventListener('dblclick', e => {e.preventDefault})},//prevents zoom
  unmounted(){clearInterval(this.timer);},//turns off timer
  methods:{
    config(){
        this.screen= 'config'
    },
     animatePoints(){
        const score = document.getElementById('game-points');
            score.className += ' animate-minimize';
                console.log('answer empty ')
                setTimeout(() => {
                    console.log('time is 400');
                    score.classList.remove('animate-minimize')
                }, 400);
    },

    play(){
          this.screen = 'play';
          this.newQuestion();
          this.startTimer();
    },
    clearAnswer(){
        this.input = ''
    },
    setInput(value) {
        this.input += String(value);
        this.input = String(Number(this.input));
        this.answered = this.checkAnswer(this.input, 
                                        this.operation,
                                        this.operands);
        if (this.answered) {
          setTimeout(this.newQuestion, 300);
          this.animatePoints()
          this.score++;
        }
      },
    getRandomNumbers(operator, low, high){
        let num1 = randInt(low, high);
        let num2 = randInt(low, high);
        const numHigh = Math.max(num1, num2);
        const numLow = Math.min(num1, num2);

        if(operator === '-'){
            num1 = numHigh;
            num2 = numLow;
        }

        if(operator === '/'){
            if(num2 === 0){
                num2 = randInt(1, high);
            }
            num1 = (num1 * num2);
        }
        return {num1, num2};
    },

    newQuestion(){
        this.input = '';
        this.answered = false;
        this.operands = this.getRandomNumbers(
            this.operation, 0 , this.maxNumber);
    },
    checkAnswer(userAnswer, operation, operands){
        if(isNaN(userAnswer)) return false;

        let correctAnswer;
        switch(operation){
            case '+':
            correctAnswer = operands.num1 + operands.num2;
            break
            case '-':
            correctAnswer = operands.num1 - operands.num2;
            break
            case 'x':
            correctAnswer = operands.num1 * operands.num2;
            break
            default : //division
            correctAnswer = operands.num1 / operands.num2;
            break
        }
        return (parseInt(userAnswer) === correctAnswer);
    },
    startTimer(){
      window.addEventListener('keyup', this.handleKeyUP);
      // console.log(this.timeLeft);
      this.timeLeft = this.gameLength;
      // console.log(this.timeLeft);
      if(this.timeLeft > 0){
        this.timer = setInterval(() =>{
          this.timeLeft--;
          // console.log(this.timeLeft);
          if(this.timeLeft === 0){
            clearInterval(this.timer);
            window.removeEventListener('keyup', this.handleKeyUP)
          }
        }, 1000)
      }

    },
    restart(){
      this.score = 0;
      this.startTimer();
      this.newQuestion();
    },
    handleKeyUP(e){
      e.preventDefault();
      if(e.keyCode === 32 || e.keyCode === 13 ){
        this.clearAnswer();
      }else if (e.keyCode === 8){
        this.input = this.input.substring(0, this.input.length-1);
      }else if (!isNaN(e.key)){
        this.setInput(e.key);
      }
    }


  },

  components: {
    TheHeader,
    SelectInput,
    PlayButton,
    Score,
    Timer,
    Equation
}
}
</script>

<style scoped>
  #main-container {
    margin: auto;
    width: 380px;
  }

  button.number-button {
    border-radius: .25em;
    font-size: 3em;
    height: 2em;
    margin: .1em;
    text-align: center;
    width: 2em;
  }

  #clear-button {
    border-radius: .25em;
    font-size: 3em;
    height: 2em;
    margin: .1em;
    text-align: center;
    width: 4.2em;
  }
  
  #scoreboard {
    font-size: 1.5em;
  }




</style>