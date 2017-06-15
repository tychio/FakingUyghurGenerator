<template>
  <div id="app">
    <header>
      <img class="logo" width="50" src="./assets/logo.png">
    </header>
    <button @click="collect">Collect</button>
    <button>Download</button>
    <Painter @done="got" :word="currentWord"></Painter>
    {{images}}
    <Vocabulary :list="words"></Vocabulary>
  </div>
</template>

<script>
import Painter from './components/Painter'
import Vocabulary from './components/Vocabulary'
import * as words from './assets/ugyhur.json'
import * as _ from 'lodash'

const wordList = _.map(words, word => {
  return {
    code: word,
    text: unescape(word),
    checked: false
  }
})

export default {
  name: 'app',
  data: function () {
    return {
      words: wordList,
      currentWord: '',
      images: []
    }
  },
  computed: {
    checkedWords: function () {
      return _.filter(this.words, word => word.checked)
    }
  },
  methods: {
    got: function (imageBase64) {
      this.images.push(imageBase64.split('data:image/png;base64,')[1])
    },
    collect: function () {
      this.images = [];
      _.each(this.checkedWords, word => {
        this.currentWord = word;
      })
    }
  },
  components: {
    Painter,
    Vocabulary
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

header {
  overflow: hidden;
  height: 70px;
  background: #eee;
}

.logo {
  float: left;
  margin: 10px;
  width: 50px;
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
