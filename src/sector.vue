<template lang="pug">
  path(
    :d="path" 
    :fill="color"
  )
</template>

<script>
import hsvToRgb from './hsvToRgb'

export default {
  props: {
    size: {
      type: Number,
      required: true
    },
    startAngle: {
      type: Number,
      required: true
    },
    endAngle: {
      type: Number,
      required: true
    },
    outerRadius: {
      type: Number,
      required: true
    },
    innerRadius: {
      type: Number,
      required: true
    }
  },
  computed: {
    path () {
      const { startAngle, endAngle, outerRadius, innerRadius } = this

      const p1 = this.pointOnCircle(outerRadius, startAngle)
      const p2 = this.pointOnCircle(outerRadius, endAngle)
      const p3 = this.pointOnCircle(innerRadius, endAngle)
      const p4 = this.pointOnCircle(innerRadius, startAngle)

      const longArcFlag = +(endAngle < startAngle ? startAngle - endAngle > Math.PI : startAngle - endAngle > -Math.PI)

      return `M ${p1.x},${p1.y}
        A ${outerRadius},${outerRadius} 0 ${longArcFlag} 1 ${p2.x} ${p2.y}
        L ${p3.x},${p3.y}
        A ${innerRadius},${innerRadius} 0 ${longArcFlag} 0 ${p4.x} ${p4.y}
        Z`
    },
    color () {
      const { startAngle } = this
      return hsvToRgb(Math.abs(startAngle) * 180 / Math.PI)
    }
  },
  methods: {
    pointOnCircle (radius, angle) {
      const { size } = this
      
      const center = {
        x: size / 2,
        y: size / 2
      }

      return {
        x: center.x + radius * Math.cos(angle),
        y: center.y - radius * Math.sin(angle)
      }
    }
  }
}
</script>