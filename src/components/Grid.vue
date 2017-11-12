<template>
  <div class="grid" v-bind:style="styleObject">
  </div>
</template>

<script>
import Color from 'color-js'

export default {
  name: 'Grid',
  props: {
    posX: Number,
    posY: Number,
    gZoom: Number,
    prx: Number,
    pry: Number
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      color: '#000',
      top: -500,
      left: -500,
      w: 20,
      h: 20,
      px: 0,
      py: 0,
      pz: 0,
      spx: 0,
      spy: 0,
      spz: 0,
      ox: 0,
      oy: 0,
      oz: 0,
      op: 0,
      vx: 3 * Math.PI / 180,
      vy: 0,
      vz: 0
    }
  },
  created: function () {
    this.color = new Color({
      red: Math.random(),
      green: Math.random(),
      blue: Math.random(),
      alpha: 1
    }).toCSS()
    this.px = this.prx
    this.py = this.pry
    this.spx = this.gZoom * this.px
    this.spy = this.gZoom * this.py
    this.spz = this.gZoom * this.pz
    this.ox = window.innerWidth / 2
    this.oy = window.innerHeight / 2
    this.oz = 210
    this.op = 400
    console.log(this.ox, this.oy)
  },
  mounted: function () {
    this.$parent.$on('enterFrame', this.rotate)
  },
  methods: {
    mov: function (x, y) {
      this.top = y
      this.left = x
    },
    rot: function () {
      let tempa, tempb
      tempa = this.py * Math.cos(this.vx) - this.pz * Math.sin(this.vx)
      tempb = this.pz * Math.cos(this.vx) + this.py * Math.sin(this.vx)
      this.py = tempa
      this.pz = tempb

      tempa = this.px * Math.cos(this.vy) + this.pz * Math.sin(this.vy)
      tempb = this.pz * Math.cos(this.vy) - this.px * Math.sin(this.vy)
      this.px = tempa
      this.pz = tempb

      tempa = this.px * Math.cos(this.vz) - this.py * Math.sin(this.vz)
      tempb = this.py * Math.cos(this.vz) + this.px * Math.sin(this.vz)
      this.px = tempa
      this.py = tempb
    },
    spread: function () {
      this.spx = this.gZoom * this.px
      this.spy = this.gZoom * this.py
      this.spz = this.gZoom * this.pz
    },
    perspective: function () {
      let z
      z = this.spz + this.oz
      if (z > 0) {
        this.left = (this.spx * this.op / z) + this.ox - this.w / 2
        this.top = -1 * (this.spy * this.op / z) + this.oy - this.h
      }
    },
    vangle: function () {
      let tempa, tempb
      tempa = (this.ox - this.posY) / 20
      if (tempa > 12) {
        tempa = 12
      } else if (tempa < -12) {
        tempa = -12
      }
      this.vx = -1 * tempa * Math.PI / 180
      tempb = (this.oy - this.posX) / 20
      if (tempb > 12) {
        tempb = 12
      } else if (tempb < -12) {
        tempb = -12
      }
      this.vy = -1 * tempb * Math.PI / 180
    },
    rotate: function () {
      this.rot()
      this.spread()
      this.perspective()
      this.vangle()
    }
  },
  computed: {
    styleObject: function () {
      let z = this.spz + this.oz
      return {
        left: this.left + 'px',
        top: this.top + 'px',
        borderRadius: '4px',
        border: '1px solid' + this.color,
        width: this.w * this.op / z + 'px',
        height: this.h * this.op / z + 'px',
        zIndex: Math.floor(1000 - this.spz),
        opacity: 0.8
      }
    }
  }
}
</script>

<style scoped>
h1, h2 {
  font-weight: normal;
}

.grid {
  position: absolute;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
