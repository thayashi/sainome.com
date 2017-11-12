<template>
  <div id="app" 
    @mousemove="onMousemove"
    @click="onMouseclick"
    v-bind:style="styleObject" >
    <div class="logo">
      <h1><img src="./assets/logo.svg" alt="sainome.com" width="184" height="22"></h1>
    </div>
    <div class="info"><a href="mailto:info@sainome.com">info@sainome.com</a></div>
    <div v-for="(griditema, i) in grids">
      <div v-for="(griditemb, j) in griditema">
        <Grid
          v-bind:pos-x="posX"
          v-bind:pos-y="posY"
          v-bind:g-zoom="gZoom"
          v-bind:prx="grids[i][j][0]"
          v-bind:pry="grids[i][j][1]"
        />
      </div>
    </div>
    <div class="metrics">
      <div>MOUSE X:{{ posX }}</div>
      <div>MOUSE Y:{{ posY }}</div>
      <div>CSSColor{{ tweenedCSSColor }}</div>
      <div>Zoom:{{ gZoom }}</div>
    </div>
  </div>
</template>

<script>
import Grid from './components/Grid'
import TWEEN from '@tweenjs/tween.js'
import Color from 'color-js'

export default {
  name: 'app',
  components: {
    Grid
  },
  data () {
    return {
      msg: 'root',
      count: 0,
      number: 0,
      animatedNumber: 0,
      color: {
        red: 1,
        green: 1,
        blue: 1,
        alpha: 1
      },
      tweenedColor: {},
      posX: 0,
      posY: 0,
      gZoom: 2,
      gZoomBase: 0.8,
      gX: 500,
      gY: 500,
      gZ: 200,
      gP: 400,
      accel: 35,
      slow: 1.07,
      vx: 0,
      vxPlus: 0.2,
      grids: []
    }
  },
  created: function () {
    this.color = new Color({
      red: Math.random(),
      green: Math.random(),
      blue: Math.random(),
      alpha: 1
    })
    this.tweenedColor = Object.assign({}, this.color)
    const sep = 35
    const num = 7
    const amplitude = Math.floor(num / 2)
    let _grid = new Array(num)
    for (let i = 0; i < num; i++) {
      _grid[i] = new Array(num)
    }
    for (let i = 0; i < num; i++) {
      for (let j = 0; j < num; j++) {
        _grid[i][j] = [sep * (-amplitude + j) + 1, sep * (amplitude - i) - 1]
      }
    }
    this.grids = _grid
  },
  mounted: function () {
    console.log('msg is: ' + this.msg)
    requestAnimationFrame(this.enterFrame)
  },
  computed: {
    count2: function () {
      return this.count + 100
    },
    tweenedCSSColor: function () {
      return new Color({
        red: this.tweenedColor.red,
        green: this.tweenedColor.green,
        blue: this.tweenedColor.blue,
        alpha: this.tweenedColor.alpha
      }).toCSS()
    },
    tweenedCSSColor2: function () {
      return new Color({
        red: this.tweenedColor.red,
        green: this.tweenedColor.green,
        blue: this.tweenedColor.blue,
        alpha: this.tweenedColor.alpha
      }).shiftHue(30).darkenByRatio(0.3).toCSS()
    },
    styleObject: function () {
      return {
        background: `linear-gradient(to bottom, ${this.tweenedCSSColor}, ${this.tweenedCSSColor2})`
      }
    }
  },
  methods: {
    enterFrame: function (timestamp) {
      this.count ++
      this.restoreZoom()
      this.$emit('enterFrame')
      requestAnimationFrame(this.enterFrame)
    },
    restoreZoom: function () {
      this.vx = (this.vx + (this.gZoomBase - this.gZoom) / this.accel) / this.slow
      this.gZoom += this.vx
    },
    onMousemove: function (event) {
      this.posX = event.clientX + 20
      this.posY = event.clientY + 20
    },
    onMouseclick: function (event) {
      this.number += 100
      this.vx += this.vxPlus
      this.color = new Color({
        red: Math.random(),
        green: Math.random(),
        blue: Math.random(),
        alpha: 1
      }).toRGB()
      console.log('clicked')
    }
  },
  watch: {
    number: function (newValue, oldValue) {
      function animate () {
        if (TWEEN.update()) {
          requestAnimationFrame(animate)
        }
      }
      new TWEEN.Tween({ tweeningNumber: oldValue })
        .easing(TWEEN.Easing.Quadratic.Out)
        .to({ tweeningNumber: newValue }, 500)
        .onUpdate((twn) => {
          this.animatedNumber = Number(twn.tweeningNumber.toFixed(0))
        })
        .start()
      animate()
    },
    color: function () {
      function animate () {
        if (TWEEN.update()) {
          requestAnimationFrame(animate)
        }
      }
      new TWEEN.Tween(this.tweenedColor)
        .to(this.color, 750)
        .start()
      animate()
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;;
}
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
  overflow: hidden;
}

.metrics {
  position: absolute;
  bottom: 5px;
  left: 5px;
  font-size: 10px;
}

.logo {
  position: absolute;
  top: 15px;
  left: 15px;
  opacity: 0.6;
}

.logo h1 {
  margin: 0;
  padding: 0;
}

.logo h1 img{
  display: block;
}

.info {
  position: absolute;
  bottom: 15px;
  right: 15px;
  opacity: 0.8;
}

</style>
