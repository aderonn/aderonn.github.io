<template>
  <div class="wrapper-video" v-if="step === 'modeselect'">
    <video autoplay muted loop onloadstart="volume=0.15" id="video" @click="unmute">
      <source type="video/mp4" src="./assets/dobro.mp4">
    </video>
    <div class="logo">
      <img src="./assets/logo.png" height="160" width="1000" alt="">
    </div>
    <div class="training-choice">
      <a class="big-button" @click="openTracking">
        <span></span>
        <span></span>
        <span></span>
        <span></span>
        ЖОСКО ПОТРЕКАТЬ
      </a>
      <a class="big-button" @click="openReaction">
        <span></span>
        <span></span>
        <span></span>
        <span></span>
        ХАНТАРЕС ПИК ТРЕНИНГ (WIP)
        </a>
    </div>
    <div class="tooltip">Нажми в любую часть экрана для жесткого навала!<br>Если что то не робит - F5</div>
  </div>
  <div class="wrapper" v-else-if="step === 'tracking'">
    <div class="header">
      <div class="back-button" @click="goBack">&#8249;</div>
      <div class="score">Score : {{score}}</div>
    </div>

    <div class="difficulty-selector" v-if="isSelectorOpened">
      <div class="difficulty-selector-text">Выбери сложность:</div>
      <div class="difficulty" @click="difficultyChoose(1)">
        <img src="./assets/bronze.png" height="60" width="60" alt="">
        <div class="difficulty-text">Чушок</div>
      </div>
      <div class="difficulty" @click="difficultyChoose(2)">
        <img src="./assets/gold.png" height="60" width="60" alt="">
        <div class="difficulty-text">Шелуха</div>
      </div>
      <div class="difficulty" @click="difficultyChoose(3)">
        <img src="./assets/diamond.png" height="60" width="60" alt="">
        <div class="difficulty-text">Позер</div>
      </div>
      <div class="difficulty" @click="difficultyChoose(4)">
        <img src="./assets/radiant.png" height="60" width="60" alt="">
        <div class="difficulty-text">Еблан</div>
      </div>
    </div>

    <div class="time-remaining" v-if="isDifficultyChosen">Time left: {{timeRemaining}}</div>
    <div class="countdown" v-if="isDifficultyChosen && countdownTimer !== 0">{{countdownTimer}}</div>
    <tracking v-if="isDifficultyChosen && countdownTimer === 0 && isGamePlaying"
              :isDifficultyChosen="isDifficultyChosen"
              :countdownTimer="countdownTimer"
              :timeRemaining="timeRemaining"
              @score="score += $event"
              :difficulty="difficulty"/>

    <div class="results" v-if="isResultsShown">
      <p>Ты набрал : {{score}}</p>
      <div v-if="isNewRecord">
        <div class="new-record">
          <h1>New Record!</h1>
        </div>
        <audio autoplay onloadstart="volume=0.2">
          <source src="./assets/doma.mp3" type="audio/mp3">
        </audio>
      </div>
      <p>В среднем ты набираешь {{ averageScore }} из учета {{ entries }} попыток</p> 
      <div class="restart" @click="restart">Похуй еще одну</div>
      <div class="restart" @click="goBack">Нахуй</div>
      <div class="restart"><a href="https://vk.com/id0">я щас ему въебу</a></div>
    </div>

  </div>
  
  <div class="wrapper" v-else-if="step === 'reaction'">
    <div class="header">
      <div class="back-button" @click="goBack">&#8249;</div>
      <div class="score">Score : {{score}}</div>
    </div>

    <div class="difficulty-selector" v-if="isSelectorOpened">
      <div class="difficulty-selector-text">Выбери сложность:</div>
      <div class="difficulty" @click="difficultyChoose(1)">
        <img src="./assets/bronze.png" height="60" width="60" alt="">
        <div class="difficulty-text">Парализованный</div>
      </div>
      <div class="difficulty" @click="difficultyChoose(2)">
        <img src="./assets/gold.png" height="60" width="60" alt="">
        <div class="difficulty-text">Пенсионер</div>
      </div>
      <div class="difficulty" @click="difficultyChoose(3)">
        <img src="./assets/diamond.png" height="60" width="60" alt="">
        <div class="difficulty-text">На рандом дал еблан</div>
      </div>
      <div class="difficulty" @click="difficultyChoose(4)">
        <img src="./assets/radiant.png" height="60" width="60" alt="">
        <div class="difficulty-text">Inhuman reaction</div>
      </div>
    </div>
    <div class="time-remaining" v-if="isDifficultyChosen">Time left: {{timeRemaining}}</div>
    <div class="countdown" v-if="isDifficultyChosen && countdownTimer !== 0">{{countdownTimer}}</div>
    
    <reaction :timeRemaining="timeRemaining"
              :score="score"
              :isGamePlaying="isGamePlaying"/>
  </div>
</template>




<script>
import tracking from './tracking.vue'
import reaction from './reaction.vue'

export default {
  components: {
    tracking,
    reaction
  },
  
  data() {
    return {
      step: 'modeselect',
      score: 0,
      difficulty: 0,
      isSelectorOpened: false,
      isDifficultyChosen: false,
      timeRemaining: '30',
      countdownTimer: 3,
      isResultsShown: false,
      isGamePlaying: false,
      isNewRecord: false,
      averageScore: 0,
      entries: 0,
      scores: [],
      maxScore: 0
    }
  },

  mounted() {
    
  },

  computed: {
    
  },

  watch: {
    isDifficultyChosen (newValue) {
      if (newValue && this.countdownTimer !== 0) {
        let timer = setInterval(() => {this.countdownTimer--}, 1000)
        setTimeout(() => clearInterval(timer), 3000)
      }
      this.countdownTimer = 3
    },

    countdownTimer (newValue) {
      if(newValue === 0 && this.isDifficultyChosen) {
        this.timeRemaining = 30
        this.isGamePlaying = true
      }
    },

    timeRemaining (newValue) { 
      if(this.isDifficultyChosen && newValue > 0 && this.countdownTimer === 0){
        setTimeout(() => this.timeRemaining--, 1000)
      } else if (newValue === 0){
        this.timeRemaining = 'все нахуй'
        this.isGamePlaying = false
        this.isResultsShown = true
        this.isDifficultyChosen = false
      }
    },

    isResultsShown (newValue) {
      if (this.scores.length > 1 && this.score !== 0) {
        this.scores.push(this.score)
        this.scores = this.scores.filter(item => item !== 0)
        if (this.score > Math.max(...this.scores)) {
          this.isNewRecord = true
          this.maxScore = this.score
        }
        this.entries += 1
      } else if (this.score !== 0) {
        if (this.score > Math.max(...this.scores)) {
          this.isNewRecord = true
          this.maxScore = this.score
        }
        this.scores.push(this.score)
        this.scores = this.scores.filter(item => item !== 0)
        this.entries += 1
      }
    },

    entries (newValue) {
      this.averageScore = this.scores.reduce((prev, score) => prev + score, 0) / newValue
    },


  },

  methods: {
    openTracking () {
      this.step = 'tracking'
      this.isSelectorOpened = true
    },

    openReaction () {
      this.step = 'reaction'
      this.isSelectorOpened = true
    },

    goBack () {
      this.step = 'modeselect',
      this.score = 0,
      this.difficulty = 0,
      this.isSelectorOpened = false,
      this.isDifficultyChosen = false,
      this.isResultsShown = false,
      this.timeRemaining = '30'
      this.isNewRecord = false
    },

    difficultyChoose (value) {
      this.difficulty = value
      this.isSelectorOpened = false
      this.isDifficultyChosen = true
    },

    restart() {
      this.score = 0,
      this.scores = this.scores.filter(item => item !== 0)
      this.isResultsShown = false,
      this.timeRemaining = '30'
      this.isDifficultyChosen = true
      this.isNewRecord = false
    },

    unmute() {
      document.getElementById("video").muted = false
    }
  }
}
</script>

<style>
</style>
