<template>
  <div class="Painter">
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
    this.ctx.textAlign = 'center'
    this.ctx.textBaseline = 'middle'
  },
  watch: {
    word: function () {
      const length = this.word.code.match(/%/g).length;
      this.ctx.font = 450-length*30 + 'px Avenir'
      this.ctx.clearRect(0, 0, this.width, this.height)
      this.ctx.fillText(this.word.text, this.width/2, this.height/2)
      this.$emit('done', this.$refs.canvas.toDataURL());
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
</style>
