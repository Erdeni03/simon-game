<template>
  <div id="app">
    <h1>Simon Game</h1>
    <h2>Раунд: {{ round }}</h2>
    <span class="message" v-if="endGame"
      >Вы проиграли на {{ round }} раунде!</span
    >
    <div class="wrap">
      <div
        class="box top-left-box"
        ref="box1"
        @click="checkSelectElement(1)"
      ></div>
      <div
        class="box top-right-box"
        ref="box2"
        @click="checkSelectElement(2)"
      ></div>
    </div>
    <div class="wrap">
      <div
        class="box bottom-left-box"
        ref="box3"
        @click="checkSelectElement(3)"
      ></div>
      <div
        class="box bottom-right-box"
        ref="box4"
        @click="checkSelectElement(4)"
      ></div>
    </div>
    <div class="disabled-field" v-if="isPlayOrder || !isStartGame"></div>
    <button class="btn" @click="start">Start Game</button>
    <div class="game-mode">
      <h4>Game Mode</h4>
      <div>
        <input
          type="radio"
          id="eazy"
          name="mode"
          v-model.number="level"
          value="1500"
        />
        <label for="eazy">Легкий</label><br />
        <input
          type="radio"
          id="normal"
          name="mode"
          v-model.number="level"
          value="1000"
        />
        <label for="normal">Нормальный</label><br />
        <input
          type="radio"
          id="hard"
          name="mode"
          v-model.number="level"
          value="500"
        />
        <label for="hard">Сложный</label><br />
      </div>
    </div>
    <div>
      <audio ref="audio1" src="@/assets/sounds/1.mp3"></audio>
      <audio ref="audio2" src="@/assets/sounds/2.mp3"></audio>
      <audio ref="audio3" src="@/assets/sounds/3.mp3"></audio>
      <audio ref="audio4" src="@/assets/sounds/4.mp3"></audio>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      startGame: false,
      round: 0,
      level: 1500,
      currentIndex: 0,
      order: [],
      orderPlayer: [],
      isPlayOrder: false,
      isStartGame: false,
      endGame: false
    }
  },
  methods: {
    start() {
      if (!this.isStartGame) {
        this.round = 0
      }
      this.isStartGame = true
      this.round += 1
      this.isPlayOrder = true
      this.currentIndex = 0
      this.getRandomOrder()
      this.endGame = false
      let idx = 0
      let order = this.order
      let playGame = this.playGame
      let interval = setInterval(function() {
        if (order.length - 1 >= idx) {
          playGame(idx)
          idx++
        } else {
          clearInterval(interval)
        }
      }, this.level)
    },
    getRandomOrder() {
      this.order.push(Math.floor(Math.random() * 4) + 1)
    },
    playGame(idx) {
      if (idx) {
        let previousElement = this.$refs['box' + this.order[idx - 1]]
        previousElement.classList.remove('active')
      }
      let currentEl = this.$refs['box' + this.order[idx]]
      let sound = this.$refs['audio' + this.order[idx]]
      currentEl.classList.add('active')
      setTimeout(() => currentEl.classList.remove('active'), 200)
      this.playSound(sound)

      if (this.order.length - 1 === idx) {
        setTimeout(() => currentEl.classList.remove('active'), this.level)
        this.isPlayOrder = false
      }
    },
    checkSelectElement(element) {
      if (this.isStartGame) {
        this.orderPlayer.push(element)
        let sound = this.$refs['audio' + [element]]
        this.playSound(sound)
        if (
          this.orderPlayer[this.currentIndex] === this.order[this.currentIndex]
        ) {
          this.currentIndex += 1
          if (this.orderPlayer.length === this.order.length) {
            this.orderPlayer = []
            setTimeout(this.start, 500)
          }
        } else {
          this.stopGame()
        }
      }
    },
    playSound(sound) {
      sound.pause()
      sound.currentTime = 0
      sound.play()
    },
    stopGame() {
      this.endGame = true
      this.order = []
      this.orderPlayer = []
      this.isStartGame = false
    }
  }
}
</script>

<style>
body .active {
  background-color: #fff;
}
body {
  background-color: rgb(189, 189, 189);
}
h1 {
  text-align: center;
}
h2 {
  text-align: center;
}

.wrap {
  text-align: center;
}

.box {
  width: 150px;
  height: 150px;
  display: inline-block;
}
.box:hover {
  background-color: #fff;
}
.bottom-left-box {
  background-color: red;
  border-bottom-left-radius: 100%;
  margin-right: 5px;
}
.bottom-right-box {
  background-color: yellow;
  border-bottom-right-radius: 100%;
}
.top-left-box {
  background-color: blue;
  border-top-left-radius: 100%;
  margin-right: 5px;
}
.top-right-box {
  background-color: green;
  border-top-right-radius: 100%;
}

.btn {
  width: 200px;
  height: 50px;
  display: block;
  margin: auto;
  margin-top: 40px;

  cursor: pointer;
}

.game-mode {
  margin-top: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.game-mode h4 {
  margin: 20px 0 10px 0;
}
body .disabled-field {
  width: 333px;
  height: 333px;
  position: absolute;
  top: 13%;
  left: 42%;
}
body .message {
  display: block;
  text-align: center;
  margin: 10px 0 20px 0;
}
</style>
