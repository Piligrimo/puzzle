<template>
  <div id="app">
    <div class="display-container">
      <div class="side left-side">
        <img class="mob" :src="mob.image" v-for="(mob, i) in left" :key="i"  @click="putInBoat(mob, 'left')"/>
      </div>
      <div class="river" :class="{right: boatPosition !== 'left' }">
        <div class="boat">
          <div class="mob-container">
            <img class="mob-in-boat" :src="mob.image" v-for="(mob, i) in boat" :key="i" @click="getOffBoat(mob)"/>
          </div>
          <div class="boat-bg"/>
        </div>
      </div>
      <div class="side right-side">
        <img class="mob" :src="mob.image" v-for="(mob, i) in right" :key="i"  @click="putInBoat(mob, 'right')"/>
      </div>
    </div>
    <div class="actions">
      <div class="boat-display">
        <div @click="getOffBoat(mob)" v-for="(mob, i) in boat" :key="i" >
          <img class="mob" :src="mob.image" >
          <div class="close"/>
        </div>
      </div>
      <div class="button send" :class="{right: boatPosition !== 'left' }" @click="sendBoat"/>
      <div class="button reset" @click="reset"/>
    </div>

    <div v-if="gameOver" class="game-over" @click="reset">
      <h1 class="game-over__text">Смэрть</h1>
      <p class="game-over__text">Тапните, чтоб начать заново</p>
    </div>
    <div v-if="allRight" class="game-over dark"><win-screen/></div>
  </div>
</template>

<script>
import WinScreen from './components/WinScreen.vue'
class Mob {
  constructor(type, id, img, imgDed) {
    this.id = id
    this.isEvil = type !== 0
    this.image = `${img}`
    this.deadImage = `${imgDed}`
  }
}

export default {
  name: 'App',
  components: {
    WinScreen,
  },
  data() {
    return {
      left: [],
      right: [],
      boat:[],
      boatPosition: 'left',
      gameOver: false,
    }
  },
  created() {
    this.reset();
  },
  computed: {
    allRight() {
      return this.right.length >= 6
    }
  },
  methods: {
    reset () {
      this.boat = []
      this.left = [
        new Mob(0, 'Sina', 'Sina.png','SinaDead.png'),
        new Mob(0, 'Armen', 'Armen.png','ArmenDead.png'),
        new Mob(0, 'Letta', 'Letta.png','LettaDead.png'),
        new Mob(1, 'B1', 'Bomj1.png','Bomj1.png'),
        new Mob(1, 'B2', 'Bomj2.png','Bomj2.png'),
        new Mob(1, 'B3', 'Bomj3.png','Bomj3.png'),
      ]
      this.right = []
      this.boatPosition = 'left'
      this.gameOver = false;
    },
    putInBoat(mob, side) {
      if (this.boatPosition !== side) return
      if (this.boat.length >= 2) return
      this[side] = this[side].filter(({id}) =>  id !== mob.id)
      this.boat.push(mob)
    },
    getOffBoat(mob) {
      this.boat = this.boat.filter(({id}) =>  id !== mob.id)
      this[this.boatPosition].push(mob)
    },
    sendBoat() {
      if (!this.boat.length) return
      this.boatPosition = this.boatPosition === 'left' ? 'right' : 'left'
      for (let mob of this.boat) {
        this[this.boatPosition].push(mob)
      }
      this.boat = []
      this.tryToKill(this.left)
      this.tryToKill(this.right)
    },
    tryToKill(side)  {
      const evilGuys = side.filter(({isEvil}) => isEvil)
      const kindGuys = side.filter(({isEvil}) => !isEvil)
      console.log(evilGuys.length > kindGuys.length)
      if (evilGuys.length > kindGuys.length) {
        for (let guy of kindGuys) {
          this.gameOver = true;
          this.$set(guy, 'image',  guy.deadImage )
        }
      }
    }
  },
}
</script>

<style>

.display-container {
  display: flex;
  justify-content: space-between;
  background-image: url('assets/water.png');
  background-size: 30%;
  width: 100%;
  height: 450px;
}
.side  {
  width: 100px;
  padding: 20px;
  background-image: url('assets/grass.png');
  background-size: 90%;
  background-color: rgb(55, 197, 55);
}

.left-side {
  border-right: 3px solid #0A6621;
  border-top: 3px solid #0A6621;
  border-radius: 0px 30px 0px 0px;
}
.right-side {
  border-left: 3px solid #0A6621;
  border-top: 3px solid #0A6621;
  border-radius: 30px 0px  0px  0px;
}

.river {
  width: inherit;
  padding: 1rem 0;
  display: flex;
}

.right {
  transform: scaleX(-1);
}

.mob-container {
  display: flex;
  justify-content: flex-start;
  padding-left: 50px;
}

.emoji {
  font-size: 34px;
}

.mob {
  height: 60px;
}
.mob-in-boat {
  margin-top: -15px;
  height: 60px;
}

.game-over {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  text-align: center;
  z-index: 15;
  padding-top: 150px;
}

.dark {
  background-color: rgba(53, 53, 53, 0.651);
}

.boat {
  height: 100px;
  width: 150px;
}

.boat-bg {
  background-image: url('assets/Ольга в лодке.png');
  background-size: contain;
  z-index: 10;
  width: inherit;
  height: inherit;
  position: absolute;
  top: 0;
}

.game-over__text {
  width: 180px;
  margin: 1rem auto;
  color: rgb(255, 255, 255);
  text-shadow: -1px 1px 3px #000,
    1px 1px 0 #000,
    1px -1px 0 #000,
    -1px -1px 0 #000;
}

.actions {
  display: flex;
  padding: 1rem;
  justify-content: space-between;
  align-items: center;
  border-top: 2px solid #3a3a3a;
}

.button {
  margin: 5px;
  width: 50px;
  height: 50px;
  background-size: 70%;
  background-position: center;
  background-color: #f1f1f1;
  background-repeat: no-repeat;
  border: 2px solid #000000;
  border-radius: 20px;
}

.button:active {
  opacity: .6;
  transform: translateY(2px);
}

.reset {
  background-image: url('assets/reset.png');
}
.send {
  background-image: url('assets/right-arrow.png');
}

.boat-display {
  padding: 5px 1rem;
  min-height: 80px;
  width: 150px;
  background-color: #8b8b8b;
  border: 2px solid #3a3a3a;
  border-radius: 8px 50% 50% 8px;
}

.boat-display > div {
  display: inline;
  height: 75px;
  position: relative;
}

.close {
  position: absolute;
  background-size: contain;
  background-image: url('assets/cancel.png');
  height: 20px;
  width: 20px;
  top: -50px;
  right: 0px;
}

.boat-display > div > .mob {
  height: 75px;
}


body {
  margin: 0;
  background-color: #c7c7c7;
}
</style>
