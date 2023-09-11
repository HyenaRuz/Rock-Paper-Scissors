<template>

  <div class="holder" id="app">
    <div class="field __big">
      <div class="table __long">
        <div class="round">
          <p class="title">Rounds:</p>
          <p class="values">{{ round }}</p>
        </div>
        <div class="victories">
          <p class="title">Victories:</p>
          <p class="values">{{ victories }}</p>        
        </div>
        <div class="losses">
          <p class="title">Losses:</p>
          <p class="values">{{ losses }}</p>
        </div>
      </div>
      <img id="player" :src='playerItem' alt="">
      <img id="indicator" :src='result' alt="">
      <img id="enemy" :src='currentImage' alt="">
      <div class="control __long">
        <ButtonMy class="control-Btn start" v-show="showElementStart" @executeMethod="this.startGame()">
          <p>start</p>
        </ButtonMy>
        <p class="timer" v-show="timerVisible">{{ timer }}</p>
        <ButtonMy class="control-Btn reset" v-show="showElementReset" @executeMethod="this.resetFild()">
          <p>reset</p>
        </ButtonMy>
      </div>
    </div>

    <ButtonMy id="paperBtn" @executeMethod="() => cardSelection('paper')" :disabled="!isButtonClickable">
      <img class="btnImg" src="./assets/paper.png" alt="">
    </ButtonMy>
    <ButtonMy id="rockBtn" @executeMethod="() => cardSelection('rock')" :disabled="!isButtonClickable">
      <img class="btnImg" src="./assets/rock.png" alt="">
    </ButtonMy>
    <ButtonMy id="shearsBtn" @executeMethod="() => cardSelection('shears')" :disabled="!isButtonClickable">
      <img class="btnImg" src="./assets/shears.png" alt="">
    </ButtonMy>
  </div>

</template>

<script>
import ButtonMy from './components/ButtonMy.vue';

import arrow_back from './assets/arrow_back.png';
import arrow_forward from './assets/arrow_forward.png';
import equal from './assets/equal.png';

import paper from './assets/paper.png';
import rock from './assets/rock.png';
import shears from './assets/shears.png';

export default {
  components: {
    ButtonMy,
  },
  data(){
    return {
      playerItem: "",
      enemyItem: "",
      result: "",
      currentImageKey: "",
      intervalId: null,
      showElementReset: false,
      showElementStart: true,
      isButtonClickable: false,
      timerVisible: false,
      round: 0,
      victories: 0,
      losses: 0,
      timer: 0,
      timeoutId: null,
      intervalIdTimer: null,

      cards: {
        paper: paper,
        rock: rock,
        shears: shears,
      },
      indicators: {
        arrow_back: arrow_back,
        arrow_forward: arrow_forward,
        equal: equal,
      },
    }
  },
  methods:{
    gameCounter(){          // счетчик побед. записыват победы поражения в таблицу
      if(this.result == this.indicators.arrow_back){
        this.victories++;
      } else if(this.result == this.indicators.arrow_forward){
        this.losses++;
      }
    },
    resetFild(){                        // новый раунд. сбрасывает все до начальных значений
      this.playerItem = "";
      this.enemyItem = "";
      this.result = "";
      this.showElementReset = false;
      this.showElementStart = true;
      this.currentImageKey = '';
      clearInterval(this.intervalIdTimer)
    },
    startAction(){        //начало хода игрока
      this.timerVisible =true; 
      this.timer = 1;
      this.intervalIdTimer = setInterval(() => {        //запускает таймер
        this.timer++
      }, 1000)
      this.timeoutId = setTimeout(() => {               // таймер по истечению которого если игрок не сделал ход он проиграл
        this.cardSelection(this.playerItem)             
        clearInterval(this.intervalIdTimer);
        this.timerVisible = false;
      }, 5000);
    },
    cardSelection(btn){           //ход игрока
      this.opponentMove();
      this.playerItem = this.cards[btn];
      this.pause();
      this.determiningTheWinner();
      this.showElementReset = true;
      this.isButtonClickable = false;
      this.gameCounter()
      clearTimeout(this.timeoutId);
      clearInterval(this.intervalIdTimer);
      this.timerVisible = false;

    },
    opponentMove(){                   // выбор рандомного элемента противника 
      let keys = Object.keys(this.cards);
      let randomKey = keys[Math.floor(Math.random() * keys.length)];
      this.currentImageKey = randomKey;
      this.enemyItem = this.cards[this.currentImageKey];
    },
    determiningTheWinner(){           //проверка результата хода 
      if (this.playerItem == this.enemyItem){
        this.result = this.indicators.equal;
      } else if (this.playerItem == this.cards.paper && this.enemyItem == this.cards.rock){
        this.result = this.indicators.arrow_back;
      } else if (this.playerItem == this.cards.rock && this.enemyItem == this.cards.shears){
        this.result = this.indicators.arrow_back;
      } else if (this.playerItem == this.cards.shears && this.enemyItem == this.cards.paper){
        this.result = this.indicators.arrow_back;
      } else {
        this.result = this.indicators.arrow_forward;
      }
    },
    nextElement(){        //метод с помощью которого выбирается следуйщий элемент из обьекта бля прокрутки картинки противника
      let keys = Object.keys(this.cards);
      const currentIndex = keys.indexOf(this.currentImageKey);
      const nextIndex = (currentIndex + 1) % keys.length;
      this.currentImageKey = keys[nextIndex];
    },
    pause(){                                                //останавливает интервал
      clearInterval(this.intervalId);
    },
    startGame(){             //начинает игру
      this.intervalId = setInterval(this.nextElement ,50);      //прокрутка картинок проитвника
      this.round++;
      this.showElementStart = false;
      this.isButtonClickable = true;
      this.startAction()
    },
  },
  computed: {
    currentImage() {          //присваевает картинку к айтеку хода противника
      return this.enemyItem = this.cards[this.currentImageKey];
    },
  },
}

</script>

<style lang="scss" scoped> 
  .holder {
    width: 500px;
    height: 500px;
    background: gray;
    border-radius: 10%;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);
    align-items: center;
    justify-items: center;
    position: relative;
    padding: 10%;
    .field {
      display: grid;
      background-color: white;
      width: 100%;
      height: 100%;
      border-radius: 10%;
      padding: 10px;
      align-items: center;
      justify-content: space-around;
      align-items: center;
      justify-items: center;
      grid-template-columns: repeat(3, 1fr);
      grid-template-rows: repeat(3, 1fr);
      .control{
        .timer{
          color: black;
          font-size: xx-large;
        }
        .control-Btn{
          height: 50px;
          display: flex;
          align-items: center;
          justify-content: center;
          &.start{
            background-color: darkgreen;
          }
          &.reset{
            background-color: darkred;
          }
        }
      }
      .table{
        display: flex;
        justify-content: space-around;
        align-items: flex-start;
        width: 100%;
        height: 100%;
        .title{
          color: black;
          margin: 0;
          font-size: x-large;
          border-top: 2px solid black;
          border-radius: 120px;
        }
        .values{
          margin: 10px 0 0 0;
          color: black;
          border-bottom: 2px solid black;
          border-radius: 120px;
          height: 25px;
          width: 120px;
        }
      }
    }
    .__big {
      grid-column: span 3;
      grid-row: span 2;
    }
    .__long {
      grid-column: span 3;
      grid-row: span 1;
    }
  }
</style>
