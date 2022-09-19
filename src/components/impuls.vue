<template>
  <div class="row">
    <div class="col-7 p-0">
      <div class="main-area area-left">
        <h2 class="symbol">
          {{ ticker }}
        </h2>
      </div>
    </div>

    <div class="col-5 p-0">
      <div class="container-fluid main-area area-right">
        <h1 class="speed-of-tape">{{ counter }}</h1>
        <h2 class="avg-speed">{{ speedAvg }}</h2>
      </div>
    </div>
  </div>

  <div class="container-fluid pt-3 pb-3 settings-window">
    <input type="text" class="inp" v-model="ticker" />

    <select id="exchange" class="select" v-model="pair" @change="selectChange">
      <option value="USDT">USDT</option>
      <option value="BUSD">BUSD</option>
      <option value="PERP">PERP</option>
    </select>

    <button
      @click="save()"
      class="save"
      v-bind:innerText="button_text"
    ></button>

    <div class="row">
      <div class="col-3">
        <input
          class="settings-inp"
          type="number"
          min="0"
          v-model="sot_period_avg"
        />
      </div>
      <div class="col-9">
        <p>average time period (sec)</p>
      </div>
    </div>

    <div class="row">
      <div class="col-3">
        <input
          class="settings-inp"
          type="number"
          min="0"
          v-model="alert_coef"
        />
      </div>
      <div class="col-9">
        <p>alert coefficient</p>
      </div>
    </div>

    <div class="row">
      <div class="col-3">
        <input
          class="settings-inp"
          type="number"
          min="0"
          v-model="sot_min_threshold"
        />
      </div>
      <div class="col-9">
        <p>threshold (ticks/sec)</p>
      </div>
    </div>

    <div class="row">
      <div class="col-3">
        <input
          class="settings-inp"
          type="number"
          min="0"
          v-model="sot_avg_threshold"
        />
      </div>
      <div class="col-9">
        <p>avg threshold (ticks/sec)</p>
      </div>
    </div>

    <div class="row">
      <div class="col-3">
        <input
          class="settings-inp"
          type="checkbox"
          min="0"
          v-model="show_avg_alert"
        />
      </div>
      <div class="col-9">
        <p>show avg alert</p>
      </div>
    </div>

    <div class="row">
      <div class="col-3">
        <select
          id="sound"
          class="select"
          v-model="sound"
          @change="soundChanged"
        >
          <option value="0">1</option>
          <option value="1">2</option>
          <option value="2">3</option>
          <option value="3">4</option>
          <option value="4">5</option>
          <option value="5">6</option>
          <option value="6">7</option>
          <option value="7">8</option>
          <option value="8">9</option>
          <option value="9">OFF</option>
        </select>
      </div>
      <div class="col-9">
        <p>alert sound</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.main-area {
  position: relative;
  width: 100%;
  height: 100%;
  /* background-color: aqua; */
  min-height: 100vh;
  overflow: hidden;
  border-right: 1px solid rgba(255, 255, 255, 0.25);
}

.symbol {
  position: absolute;
  top: 12px;
  left: 28px;
  font-size: 27px;
}

.speed-of-tape {
  position: absolute;
  top: 9px;
  left: 10px;
  font-size: 30px;
}
.avg-speed {
  position: absolute;
  top: 23px;
  left: 50%;
  font-size: 16px;
}
.settings-window {
  position: absolute;
  width: 100%;
  min-height: 100vh;
  background-color: #454545;
  z-index: 5000;
}
.settings-inp {
  background-color: #454545;
  width: 100%;
  color: #d3d3d3;
  padding: 0;
  border: 1px solid rgba(255, 255, 255, 0.25);
  border-radius: 5px;
  text-align: center;
  outline: none;
}

.save {
  border: none;
  margin-right: 5px;
  margin-bottom: 12px;
}

.inp {
  background-color: #454545;
  width: 30%;
  color: #d3d3d3;
  padding: 4px 8px;
  font-size: small;
  border: 1px solid rgba(255, 255, 255, 0.25);
  border-radius: 5px;
  margin-right: 12px;
  margin-bottom: 12px;
  outline: none;
}

.select {
  background-color: #454545;
  color: #d3d3d3;
  padding: 5px;
  font-size: small;
  border: 1px solid rgba(255, 255, 255, 0.25);
  border-radius: 5px;
  margin-right: 12px;
  outline: none;
  z-index: 100;
}
</style>

<script>
export default {
  name: 'MainLiqTable',
  props: {
    msg: String,
  },
  data() {
    return {
      button_text: 'Save & Start',

      pair: 'USDT',

      sound: 0,

      binanseSocket: null,
      binanceInterval: null,

      ftxSocket: null,
      ftxInterval: null,

      counter: 0,
      speedAvg: 0,
      ticker: 'BTC',
      sot_period_avg: 30,
      sot_min_threshold: 50,
      sot_avg_threshold: 20,
      alert_coef: 3,

      show_avg_alert: true,

      sounds: [
        'https://www.pacdv.com/sounds/miscellaneous_sounds/bottle_pop_2.wav',
        'https://www.pacdv.com/sounds/interface_sound_effects/beep-3.wav',
        'https://www.pacdv.com/sounds/interface_sound_effects/sound19.mp3',
        'https://www.pacdv.com/sounds/interface_sound_effects/sound27.mp3',
        'https://www.pacdv.com/sounds/interface_sound_effects/sound2.mp3',
        'https://www.pacdv.com/sounds/interface_sound_effects/sound13.mp3',
        'https://www.pacdv.com/sounds/interface_sound_effects/sound77.wav',
        'https://www.pacdv.com/sounds/interface_sound_effects/sound95.wav',
        'https://www.pacdv.com/sounds/miscellaneous_sounds/bottle_pop_1.wav',
      ],
    }
  },
  mounted() {
    this.$nextTick(() => {
      localStorage.getItem('ticker') != null
        ? (this.ticker = localStorage.getItem('ticker'))
        : (this.ticker = 'BTC')
      localStorage.getItem('pair') != null
        ? (this.pair = localStorage.getItem('pair'))
        : (this.pair = 'USDT')
      localStorage.getItem('sound') != null
        ? (this.sound = localStorage.getItem('sound'))
        : (this.sound = 0)
      localStorage.getItem('sot_period_avg') != null
        ? (this.sot_period_avg = localStorage.getItem('sot_period_avg'))
        : (this.sot_period_avg = 30)
      localStorage.getItem('sot_min_threshold') != null
        ? (this.sot_min_threshold = localStorage.getItem('sot_min_threshold'))
        : (this.sot_min_threshold = 50)
      localStorage.getItem('sot_avg_threshold') != null
        ? (this.sot_avg_threshold = localStorage.getItem('sot_avg_threshold'))
        : (this.sot_avg_threshold = 20)
      localStorage.getItem('alert_coef') != null
        ? (this.alert_coef = localStorage.getItem('alert_coef'))
        : (this.alert_coef = 3)
    })
  },
  methods: {
    selectChange(e) {
      if (e.target.options.selectedIndex > -1) {
        this.selected = e.target.options[e.target.options.selectedIndex].value
      }
    },
    soundChanged(e) {
      if (e.target.options.selectedIndex > -1) {
        this.selected = e.target.options[e.target.options.selectedIndex].value

        const audio = new Audio(this.sounds[this.selected])
        audio.play()
      }
    },
    save() {
      // Format ticker
      this.ticker = this.ticker
        .toUpperCase()
        .replace('USDT', '')
        .replace('BUSD', '')
        .replace('PERP', '')
        .replace(' ', '')
        .replace('  ', '')
        .replace('   ', '')
        .replace('    ', '')
        .replace('\n', '')
        .replace('-', '')

      // Save to localstorage
      localStorage.setItem('ticker', this.ticker)
      localStorage.setItem('pair', this.pair)
      localStorage.setItem('sound', this.sound)
      localStorage.setItem('sot_period_avg', this.sot_period_avg)
      localStorage.setItem('sot_min_threshold', this.sot_min_threshold)
      localStorage.setItem('sot_avg_threshold', this.sot_avg_threshold)
      localStorage.setItem('alert_coef', this.alert_coef)

      // Change button text
      if (this.button_text === 'Save & Start') {
        if (this.pair === 'USDT') {
          this.startBinance('usdt')
        }

        if (this.pair === 'BUSD') {
          this.startBinance('busd')
        }

        if (this.pair === 'PERP') {
          this.startFTX()
        }

        this.button_text = 'STOP'
      } else {
        try {
          this.stopBinance()
        } catch {}

        try {
          this.stopFTX()
        } catch {}

        this.button_text = 'Save & Start'
      }
    },
    stopBinance() {
      clearInterval(this.binanceInterval)
      this.binanseSocket.close()
      this.counter = 0
      this.speedAvg = 0
    },
    stopFTX() {
      clearInterval(this.ftxInterval)
      this.ftxSocket.close()
      this.counter = 0
      this.speedAvg = 0
    },

    // BINANCE
    startBinance(market) {
      let counter = 0
      const counter_arr = []

      this.binanseSocket = new WebSocket(
        `wss://fstream.binance.com/ws/${this.ticker.toLowerCase()}${market}@aggTrade`
      )

      this.binanseSocket.onmessage = () => {
        counter++
      }

      // SOT timer
      this.binanceInterval = setInterval(() => {
        this.counter = counter * 2

        counter_arr.push(counter * 2)

        if (counter_arr.length > this.sot_period_avg * 2) {
          counter_arr.shift()
        }

        const sumOfCounts = counter_arr.reduce(
          (previousValue, currentValue) => previousValue + currentValue
        )

        this.speedAvg = Math.round(sumOfCounts / counter_arr.length)

        counter = 0

        // Alert condition
        if (
          this.counter >= this.speedAvg * this.alert_coef &&
          counter_arr.length / 2 >= this.sot_period_avg &&
          this.counter >= this.sot_min_threshold &&
          this.speedAvg >= this.sot_avg_threshold
        ) {
          const audio = new Audio(this.sounds[this.sound])
          audio.play()

          const rightArea = document.querySelector('.area-right')
          rightArea.style.backgroundColor = '#d28e28'
          setTimeout(() => (rightArea.style.backgroundColor = '#333333'), 5000)
        }

        // Highlight right area when avg speed greater than threshold
        if (this.show_avg_alert && this.speedAvg >= this.sot_avg_threshold) {
          const leftArea = document.querySelector('.area-left')
          leftArea.style.borderRight = '10px solid #d28e28'
        } else {
          const leftArea = document.querySelector('.area-left')
          leftArea.style.borderRight = '1px solid rgba(255, 255, 255, 0.25)'
        }
      }, 500)
    },

    // FTX
    startFTX() {
      let counter = 0
      const counter_arr = []

      this.ftxSocket = new WebSocket('wss://ftx.com/ws/')

      this.ftxSocket.onopen = () => {
        this.ftxSocket.send(
          JSON.stringify({
            op: 'subscribe',
            channel: 'trades',
            market: `${this.ticker}-PERP`,
          })
        )
      }

      this.ftxSocket.onmessage = (msg) => {
        if (JSON.parse(msg.data).data !== undefined) {
          // Speed of tape things
          const tradesNum = JSON.parse(msg.data).data.length
          counter = counter + tradesNum
        }
      }

      // SOT timer
      this.ftxInterval = setInterval(() => {
        this.counter = counter * 2

        counter_arr.push(counter * 2)

        if (counter_arr.length > this.sot_period_avg * 2) {
          counter_arr.shift()
        }

        const sumOfCounts = counter_arr.reduce(
          (previousValue, currentValue) => previousValue + currentValue
        )

        this.speedAvg = Math.round(sumOfCounts / counter_arr.length)

        counter = 0

        // Alert condition
        if (
          this.counter >= this.speedAvg * this.alert_coef &&
          counter_arr.length / 2 >= this.sot_period_avg &&
          this.counter >= this.sot_min_threshold &&
          this.speedAvg >= this.sot_avg_threshold
        ) {
          const audio = new Audio(this.sounds[this.sound])
          audio.play()

          const rightArea = document.querySelector('.area-right')
          rightArea.style.backgroundColor = '#d28e28'
          setTimeout(() => (rightArea.style.backgroundColor = '#333333'), 5000)
        }

        // Highlight right area when avg speed greater than threshold
        if (this.show_avg_alert && this.speedAvg >= this.sot_avg_threshold) {
          const leftArea = document.querySelector('.area-left')
          leftArea.style.borderRight = '10px solid #d28e28'
        } else {
          const leftArea = document.querySelector('.area-left')
          leftArea.style.borderRight = '1px solid rgba(255, 255, 255, 0.25)'
        }
      }, 500)
    },
  },
}
</script>
