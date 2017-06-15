<template>
  <div id="app">
    <header>
      <img class="logo" width="50" src="./assets/logo.png">
    </header>
    <button @click="collect">Collect</button>
    <button @click="download">Download</button>
    <button @click="checked = !checked">{{checked? 'Unc':'C'}}hekced all</button>
    <Painter @done="got" :word="currentWord"></Painter>
    <textarea v-model="input"></textarea>
    <Vocabulary :list="words"></Vocabulary>
  </div>
</template>

<script>
import Painter from './components/Painter'
import Vocabulary from './components/Vocabulary'
import JSZip from 'jszip'
import * as words from './assets/ugyhur.json'
import * as _ from 'lodash'
import { saveAs } from 'file-saver'

export default {
  name: 'app',
  data: function () {
    return {
      input: '',
      words: [],
      currentWord: '',
      images: [],
      checked: true,
      lock: false
    }
  },
  mounted: function () {
    this.input = words;
  },
  computed: {
    firstCheckedWord: function () {
      return _.find(this.words, word => word.checked)
    }
  },
  methods: {
    got: function (data) {
      this.images.push({
        url: data.url.split('data:image/png;base64,')[1],
        text: data.text
      })
      this.do()
    },
    do: function () {
      const word = this.firstCheckedWord
      if (word) {
        this.currentWord = word;
        word.checked = false;
      } else {
        this.checked = false;
        this.lock = false;
      }
    },
    collect: function () {
      if (this.lock) {
        return;
      }
      this.images = []
      this.lock = true;
      this.do()
    },
    download: function () {
      const zip = new JSZip();
      const img = zip.folder('ugyhurWords');
      _.each(this.images, (image, index) => {
        const fileName = image.text + '.png';
        img.file(fileName, image.url, {base64: true})
      })
      zip.generateAsync({type:"blob"})
      .then(function(content) {
          saveAs(content, "ugyhurWords.zip")
      })
    }
  },
  watch: {
    checked: function (checked) {
      _.each(this.words, word => {
        word.checked = checked;
      })
    },
    input: function (data) {
      if (typeof data === 'string') {
        data = data.split(',');
      }
      if (_.isArray(data)) {
        this.words = _.map(data, word => {
          return {
            text: unescape(word),
            checked: true
          }
        })
      }
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
  width: 240px;
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

textarea {
  width: 800px;
  height: 320px;
}
</style>
