<template>
  <div @click="tap">
    <template v-if="time< 1950">
      <h1 class="game-over__text">Победа!</h1>
      <p class="game-over__text">Загрузка подсказки...</p>
      <div class="pb-container">
        <div :style="{width: `${progress}%`}" class="pb-progress"/>
      </div>
    <p class="game-over__text">{{percents}}%</p>
    </template>
    <template v-else>
      <h1 class="game-over__text error">Ошибка!</h1>
      <p class="game-over__text error">Не удалось загрузить точное значение подсказки!</p>
      <p class="game-over__text">Возможное значение подсказки:</p>
      <h1 class="game-over__text">{{hint}}</h1>
    </template>
  </div>
</template>

<script>
export default {
  name: 'WinScreen',
  data() {
    return {
      time:0
    }
  },
  mounted() {
    setInterval(() => {
      this.time+=10
    }, 50)
  },
  methods: {
    tap() {
    } 
  },
  computed: {
    progress() {
      const calc = Math.sqrt( Math.sqrt(this.time * 50000))
      return calc > 95 ? 95 : calc
    },
    percents()  {
      return Math.round(this.progress)
    },
    hint() {
      const variants = [ 13, 75, 60, 34, 58]
      const i = Math.round(this.time/80) % variants.length
      console.log(i)
      return variants[i]
    }
  }
}
</script>

<style>
  .pb-container {
    width: 180px;
    height: 20px;
    background-color: #3a3a3a;
    margin: 0 auto;
    border-radius: 5px;
    border: 2px solid #1b1b1b;
  }
  .pb-progress {
    background: #0AA415;
    background: -moz-linear-gradient(-45deg, #0AA415 0%, #81CB25 50%, #0AA415 100%);
    background: -webkit-linear-gradient(-45deg, #0AA415 0%, #81CB25 50%, #0AA415 100%);
    background: linear-gradient(135deg, #0AA415 0%, #81CB25 50%, #0AA415 100%);
    height: 100%;
    border-radius: 5px;
    transition: all .5s;
  }

  .game-over__text.error {
    color: rgb(248, 93, 93);
  }
</style>