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
      this.ctx.clearRect(0, 0, this.width, this.height)
      const wordText = this.word.text
      const rightSign = '|'
      const bottomSign = '_'
      if (wordText.indexOf(rightSign) > 0) {
        const splited = wordText.split(rightSign)
        const letter = splited[0]
        const radical = splited[1]
        this.ctx.font = '400px Avenir'
        this.ctx.fillText(letter, this.width*.35, this.height/2)
        this.ctx.fillText(radical, this.width*.8, this.height/2)
      } else if (wordText.indexOf(bottomSign) > 0) {
        const splited = wordText.split(bottomSign)
        const letter = splited[0]
        const radical = splited[1]
        this.ctx.font = '400px Avenir'
        this.ctx.fillText(letter, this.width/2, this.height*.4)
        this.ctx.fillText(radical, this.width/2, this.height)
      } else {
        const length = wordText.length
        this.ctx.font = 450-length*30 + 'px Avenir'
        this.ctx.fillText(wordText, this.width/2, this.height/2)
      }

      this.$emit('done', {
        url: this.$refs.canvas.toDataURL(),
        text: wordText
      })
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
