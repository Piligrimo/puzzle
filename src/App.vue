<template>
  <div id="app">
    <div class="display-container">
      <div class="side">
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
      <div class="side">
        <img class="mob" :src="mob.image" v-for="(mob, i) in right" :key="i"  @click="putInBoat(mob, 'right')"/>
      </div>
    </div>

    <p>Убрать из лоджки</p>
    <div>
       <img class="mob-in-boat" :src="mob.image" v-for="(mob, i) in boat" :key="i" @click="getOffBoat(mob)"/>
   </div>
    <div class="actions">
      <button class="button" @click="sendBoat">Переправить лодку</button>
      <button class="button" @click="reset">Заново</button>
    </div>

    <div v-if="gameOver &&  ! win" class="game-over" @click="reset"><h1 class="game-over__text">Смэрть</h1></div>
    <div v-if="allRight" class="game-over" @click="reset"><h1 class="game-over__text">Единственное действие</h1></div>
    <div v-if="!this.right,lengh >= 6">Диан, слыш, придумай шото</div>
  </div>
</template>

<script>

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
  },
  data() {
    return {
      left: [],
      right: [],
      boat:[],
      boatPosition: 'left',
      gameOver: false,
      win: false,
    }
  },
  created() {
    this.reset();
  },
  computed: {
    allRight() {
      return this.right.lengh >=  6
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
      if (this.right.length >= 6 ) 
        this.win = true
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
  width: 100%;
  height: 400px;
}
.side  {
  width: 100px;
  background-color: rgb(55, 197, 55);
  border-color: rgb(10, 70, 25);
}
.river {
  width: inherit;
  padding: 1rem;
  display: flex;
  background-image: url'asset/water.png');
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
  margin-top: -10px;
  height: 50px;

}

.game-over {
  position: fixed;
  background-color: rgba(53, 53, 53, 0.651);
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  text-align: center;
  z-index: 15;
}
.boat {
  height: 100px;
  width: 150px;
}

.boat-bg {
  background-image: url('assets/Ольга в лодке.png');
  z-index: 10;
  width: inherit;
  height: inherit;
  position: absolute;
  top: 0;
}

.game-over__text {
  color: rgb(255, 255, 255);
  margin-top: 150px;
}

.actions {
  display: flex;
  height: 100px;
  width: 100%;
}

.button {
  margin: 5px;
  width: 50%;
}

.reset {
  background: no-repeat;
  background-image: url('assets/reset.png');
  width: 50%;

}
.send {
  background: no-repeat;
  background-image: url('assets/right-arrow.png');
  width: 50%;
  height: auto;
}

button:{
  background: contain;
}
</style>
