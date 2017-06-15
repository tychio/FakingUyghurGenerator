<template>
  <div id="app">
    <header>
      <img class="logo" width="50" src="./assets/logo.png">
    </header>
    <Painter :word="currentWord"></Painter>
    <Vocabulary :list="words"></Vocabulary>
  </div>
</template>

<script>
import Painter from './components/Painter'
import Vocabulary from './components/Vocabulary'
import * as words from './assets/ugyhur.json'
import * as _ from 'lodash'

var wordList = _.map(words, function (word) {
  return {
    code: word,
    text: unescape(word),
    checked: true
  }
})

export default {
  name: 'app',
  data: function () {
    return {
      words: wordList,
      currentWord: ''
    }
  },
  computed: {
    checkedWords: function () {
      return _.filter(this.words, function (word) {
        return word.checked;
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
</style>
