<template>
  <div class="Painter">
    <button @click="draw()">Draw</button>
    <canvas :width="width" :height="height" ref="canvas"></canvas>
  </div>
</template>

<script>
export default {
  name: 'Painter',
  data: function () {
    return {
      ctx: null,
      width: 800,
      height: 600
    }
  },
  props: ['word'],
  mounted: function () {
    this.ctx = this.$refs.canvas.getContext('2d')
    this.ctx.font = '400px serif'
    this.ctx.textAlign = 'center'
    this.ctx.textBaseline = 'middle'
  },
  watch: {
    word: function () {
      this.ctx.clearRect(0, 0, this.width, this.height)
      this.ctx.fillText(this.word, this.width/2, this.height/2)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
canvas {
  display: block;
  margin: 10px auto;
  border: 1px solid #666;
}

button {
  width: 200px;
  height: 50px;
  line-height: 50px;
  font-size: 32px;
  margin: 10px;
  background: #ff6600;
  border: none;
}
button:hover {
  background: #ff7700;
}
button:active {
  background: #ee5500;
}
</style>
