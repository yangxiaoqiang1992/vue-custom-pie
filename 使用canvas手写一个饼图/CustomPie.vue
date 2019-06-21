<template>
  <div class="fd-echarts" >
    <canvas ref="ec" class="progress" :id="id" width="284" height="284"></canvas>
    <!-- <div class="progress" v-for="(item,index) in pieData" :key="index" :id="'progress'+(index+1)" ></div> -->
  </div>
</template>

<script>
import {
  getTotalCount,
} from '@utils/common'
export default {
  name: 'customPie',
  props: {
    pieData: Array,
    id: String,
  },
  data() {
    return {
      params: {
        center: {
          x: 142,
          y: 142,
        },
        radius: 96,
        barWidth: 30,
        anticlockwise: false, // true表示逆时针，false表示顺时针。不写默认false
      },
      canvas: null,
      ctx: null,
    }
  },
  watch: {
    pieData: {
      handler(val) {
        this.init()
      },
    },
  },
  methods: {
    init() {
      const color = [['rgba(234,255,81,0.93)', 'rgba(55,126,234,0.72)'], ['rgba(234,255,81,0.93)', 'rgba(112,55,234,0.44)'],
        ['rgba(163,255,81,0.93)', 'rgba(55,57,234,0.68)'], ['rgba(234,255,81,0.93)', 'rgba(225,55,234,0.7)'], ['rgba(234,255,81,0.93)', 'rgba(56, 255, 187, 0.7)']]
      const splitRad = this.params.barWidth * 8 / (Math.PI * 2 * this.params.radius)
      const len = this.pieData.length
      this.canvas = this.$refs.ec
      this.canvas.width = 284
      this.canvas.height = 284
      this.ctx = this.canvas.getContext('2d')
      this.ctx.translate(0, 0);
      let startAngle = Math.PI
      let endAngel = 0
      const totalCount = getTotalCount(this.pieData)

      this.pieData.forEach((item, index) => {
        const precentage = (parseInt(item.value, 10) / totalCount)
        const rad = precentage * (Math.PI * 2 - len * splitRad)
        endAngel = startAngle + rad

        this.drawRadian(startAngle, endAngel, color[index])
        startAngle = endAngel + splitRad
      });
    },

    /**
     *@param {String} startAngel 起始角度
     *@param {String} endAngel 结束角度
     *@param {String} color 绘制的颜色
     *@returns {null} void
     */
    drawRadian(startAngel, endAngel, color) {
      const {
        center, radius,
      } = this.params
      if (color instanceof Array) {
        const radient = this.ctx.createRadialGradient(0, 0, 0, 0, 0, 500)
        radient.addColorStop(0, color[0])
        radient.addColorStop(1, color[1])
        this.ctx.strokeStyle = radient
      } else {
        this.ctx.strokeStyle = color
      }
      this.ctx.save()
      this.ctx.lineWidth = 30
      this.ctx.lineCap = 'round'
      this.ctx.shadow = 'rgba(0, 0, 0,0.5)'
      this.ctx.shadowOffsetX = -3
      this.ctx.shadowOffsetY = 12
      this.ctx.shadowBlur = 24
      this.ctx.beginPath()
      this.ctx.arc(center.x, center.y, radius, startAngel, endAngel)
      this.ctx.stroke()
      this.ctx.restore()
    },
  },
}
</script>

<style scoped>
.fd-echarts{
  background: url('~@assets/images/pie-bg1.png') no-repeat left center;
  position:relative;
}
</style>


