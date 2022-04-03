<template>
  <div id="app">
    <div class="app">
      <div class="side">
        left 
        <div class="emoji" v-for="(mob, i) in left" :key="i"  @click="putInBoat(mob, 'left')"> {{mob.pic}} </div>
      </div>
      <div class="river" :class="{right: boatPosition !== 'left' }">
        boat 
        <div class="emoji" v-for="(mob, i) in boat" :key="i" @click="getOffBoat(mob)"> {{mob.pic}} </div>
      </div>
      <div class="side">
        right 
        <div class="emoji" v-for="(mob, i) in right" :key="i"  @click="putInBoat(mob, 'right')"> {{mob.pic}} </div>
      </div>
    </div>
    <button @click="sendBoat">Отправка лодки</button>
    <button @click="reset">Заново</button>
    <div v-if="gameOver" class="game-over" @click="reset"><h1 class="game-over__text">Смэрть</h1></div>
  </div>
</template>

<script>

const emojies = ['😄','😈','☠️']

class Mob {
  constructor(type, id) {
    this.id = id
    this.isEvil = type !== 0
    this.pic = emojies[type]
  }
}

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      left: [],
      right: [],
      boat:[],
      boatPosition: 'left',
      gameOver: false
    }
  },
  created() {
    this.reset();
  },
  methods: {
    reset () {
      this.boat = []
      this.left = [
        new Mob(0, 1),
        new Mob(0, 2),
        new Mob(0, 3),
        new Mob(1, 4),
        new Mob(1, 5),
        new Mob(1, 6),
      ]
      this.right = []
      this.boatPosition = 'left'
      this.gameOver = false;
    },
    putInBoat(mob, side) {
      if (this.boatPosition !== side) return
      if (this.boat.length >= 3) return
      this[side] = this[side].filter(({id}) =>  id !== mob.id)
      this.boat.push(mob)
    },
    getOffBoat(mob) {
      this.boat = this.boat.filter(({id}) =>  id !== mob.id)
      this[this.boatPosition].push(mob)
    },
    sendBoat() {
      if(this.boat.some(({isEvil}) => !isEvil)){
        this.boatPosition = this.boatPosition === 'left' ? 'right' : 'left'
        for (let mob of this.boat) {
          this[this.boatPosition].push(mob)
        }
        this.boat = []
        this.tryToKill(this.left)
        this.tryToKill(this.right)
      }
    },
    tryToKill(side)  {
      const evilGuys = side.filter(({isEvil}) => isEvil)
      const kindGuys = side.filter(({isEvil}) => !isEvil)
      console.log(evilGuys.length > kindGuys.length)
      if (evilGuys.length > kindGuys.length) {
        for (let guy of kindGuys) {
          console.log(guy)
          this.gameOver = true;
          this.$set(guy, 'pic', emojies[2])
        }
      }
    }
  },
}
</script>

<style>
.app {
  display: flex;
  justify-content: space-between;
  width: 100%;
}
.side  {
  width: 50px;
}
.river {
  width: inherit;
  padding: 1rem;
}

.right {
  text-align: right;
}
.emoji {
  font-size: 34px;
}
.game-over {
  position: fixed;
  background-color: rgba(53, 53, 53, 0.651);
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  text-align: center;
}

.game-over__text {
  color: rgb(255, 255, 255);
  margin-top: 150px;
}
</style>
