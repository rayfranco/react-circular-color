<template lang="pug">
  svg(
    :class="className"
    :width="size"
    :height="size"
    @mousemove="handleMove"
    @mouseup="handleUp"
    @touchend="handleUp"
    @click="handleClick"
  )
    
    Sector(
      v-for="sector in sectors"
      :key="sector.idx"
      :size="size"
      :startAngle="-sector.startAngle"
      :endAngle="-sector.endAngle"
      :innerRadius="innerRadius"
      :outerRadius="outerRadius"
    )

    circle(
      @mousedown="handleDown"
      @touchstart="handleDown"
      :cx="cx"
      :cy="cy"
      :r="handlerSize"
      :strokeWidth="5"
      stroke="#363636"
      fill="transparent"
    )
  </svg>
</template>


<script>
import Sector from './Sector'
import hsvToRgb from './hsvToRgb'

const EPSILON = 4

export default {
  components: {
    Sector
  },

  props: {
    size: {
      type: Number,
      default: 200
    },
    numberOfSectors: {
      type: Number,
      default: 360
    },
    className: {
      type: String,
      default: null
    },
    centerRect: {
      type: Boolean,
      default: false
    }
  },

  data () {
    return {
      handlerSize: 0,
      touched: false,
      cx: 0,
      cy: 0,
      color: null
    }
  },

  computed: {
    sectors () {
      return Array(this.numberOfSectors).fill(0).map((sector, idx) => {
        const startAngle = (idx * Math.PI / 180) * 360 / this.numberOfSectors
        const endAngle = ((idx + 1 === this.numberOfSectors ? 0 : idx + 1) * Math.PI / 180) * 360 / this.numberOfSectors
        return {
          idx,
          startAngle,
          endAngle 
        }
      })
    }
  },

  created () {
    const { size } = this
    const handlerSize = Math.floor(size / 2 * 0.3 / 2)
    const innerRadius = Math.floor(size / 2 * 0.6)
    const outerRadius = Math.floor(size / 2 * 0.9)

    // Statics
    this.handlerSize = handlerSize
    this.innerRadius = innerRadius
    this.outerRadius = outerRadius

    // Reactive
    this.cx = Math.floor((size / 2) + (innerRadius + handlerSize) * Math.cos(Math.PI)),
    this.cy = Math.floor((size / 2) - (innerRadius + handlerSize) * Math.sin(Math.PI)),
    this.color = hsvToRgb(Math.abs(Math.PI) * 180 / Math.PI)
  },
  mounted () {
    window.addEventListener('touchmove', this.preventScrolling, { passive: false })
  },
  beforeDestroy () {
    window.removeEventListener('touchmove', this.preventScrolling)
  },

  methods: {
    preventScrolling (event) {
      const { touched } = this
      if(touched) {
        event.preventDefault()
      }
    },
    handleClick (event) {
      const { size, innerRadius, outerRadius } = this

      const { x: xBlock, y: yBlock } = event.currentTarget.getBoundingClientRect()

      const x = event.clientX - xBlock - size / 2
      const y = event.clientY - yBlock - size / 2

      const angle = -Math.atan2(y, x)

      const outX = Math.abs(Math.floor((outerRadius) * Math.cos(angle)))
      const outY = Math.abs(Math.floor((outerRadius) * Math.sin(angle)))

      const inX = Math.abs(Math.floor((innerRadius) * Math.cos(angle)))
      const inY = Math.abs(Math.floor((innerRadius) * Math.sin(angle)))

      if (Math.abs(x) >= (inX - EPSILON)
          && Math.abs(x) <= (outX + EPSILON)
          || Math.abs(y) >= (inY - EPSILON)
          && Math.abs(y) <= (outY + EPSILON)) {
        this.updateHandlerPosition(angle)
      }
    },
    handleDown (event) {
      this.touched = true
      this.$emit('touch', true)
    },
    handleUp (event) {
      this.touched = false
      this.$emit('touch', false)
    },
    handleMove (event) {
      const { size } = this
      const { touched } = this

      if (touched) {
        const { x: xBlock, y: yBlock } = event.currentTarget.getBoundingClientRect()

        const evt = event.type === 'touchmove' ? event.touches[0] || event.changedTouches[0] : event
        const x = evt.clientX - xBlock - size / 2
        const y = evt.clientY - yBlock - size / 2 

        const angle = -Math.atan2(y, x)

        this.updateHandlerPosition(angle)
      }
    },
    updateHandlerPosition (angle) {
      const { size, onChange, innerRadius, handlerSize } = this

      const color = angle <= 0 ? -angle * 180 / Math.PI : (2 * Math.PI - angle) * 180 / Math.PI

      this.cx = Math.floor((size / 2) + (innerRadius + handlerSize) * Math.cos(angle)),
      this.cy = Math.floor((size / 2) - (innerRadius + handlerSize) * Math.sin(angle)),
      this.color = hsvToRgb(color)

      this.$emit('change', hsvToRgb(color))
    }
  }
}
</script>
