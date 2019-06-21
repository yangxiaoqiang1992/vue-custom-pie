## 使用canvas绘制一个饼图
<br/>canvas绘制效果如下：</br>
<br/>![示例1](https://github.com/yangxiaoqiang1992/vue-custom-pie/blob/master/%E4%BD%BF%E7%94%A8canvas%E6%89%8B%E5%86%99%E4%B8%80%E4%B8%AA%E9%A5%BC%E5%9B%BE/demo1.png)</br>
<br/>![实例2](https://github.com/yangxiaoqiang1992/vue-custom-pie/blob/master/%E4%BD%BF%E7%94%A8canvas%E6%89%8B%E5%86%99%E4%B8%80%E4%B8%AA%E9%A5%BC%E5%9B%BE/demo2.png)</br>

- init方法用于初始化canvas,设置颜色，计算各项数据所占弧度，`  const splitRad = this.params.barWidth * 8 / (Math.PI * 2 * this.params.radius)` 用于在绘制时各项之间保留一定间距，由于canvas绘制笔刷butt和round之间的区别，设置为round时按照正常的弧度计算会存在重叠，所以添加间距
```
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

```
- 绘制的主要方法
```
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
```