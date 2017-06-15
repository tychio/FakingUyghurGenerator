<template>
  <div id="app">
    <header>
      <img class="logo" width="50" src="./assets/logo.png">
      <button @click="input = ''">Clear</button>
      <button @click="inputFakingUyghur()">Faking Uyghur</button>
      <button @click="inputFakingChinese()">Faking Chinese</button>
    </header>
    <button @click="collect">Generate</button>
    <button @click="download">Download</button>
    <Painter v-show="images.length" @done="got" :word="currentWord"></Painter>
    <textarea v-model="input"></textarea>
    <button @click="checked = !checked">{{checked? 'Unc':'C'}}hekced all</button>
    <Vocabulary :list="words"></Vocabulary>
  </div>
</template>

<script>
import Painter from './components/Painter'
import Vocabulary from './components/Vocabulary'
import JSZip from 'jszip'
import * as ugyhurWords from './assets/ugyhur.json'
import * as chineseRadicals from './assets/chinese.json'
import * as _ from 'lodash'
import { saveAs } from 'file-saver'

const random = (length, start) => {
  return (Math.random()+'').slice(2,9)%length + (start || 0)
}

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
        this.currentWord = word
        word.checked = false
      } else {
        this.checked = false
        this.lock = false
      }
    },
    collect: function () {
      if (this.lock) {
        return
      }
      this.images = []
      this.lock = true
      this.do()
    },
    download: function () {
      const zip = new JSZip()
      const img = zip.folder('wordImages')
      _.each(this.images, (image, index) => {
        const fileName = image.text + '.png'
        img.file(fileName, image.url, {base64: true})
      })
      zip.generateAsync({type:"blob"})
      .then(function(content) {
          saveAs(content, "wordImages.zip")
      })
    },
    inputFakingUyghur: function () {
      this.input = ugyhurWords
    },
    inputFakingChinese: function () {
      const COUNT = 1000
      const signPosition = {'right': '|', 'bottom': '_'}
      const position = random(2) === 1 ? 'right' : 'bottom'
      const radicals = _.map(chineseRadicals[position], radical => {
        return '%u'+radical
      })
      this.input = _.range(COUNT).map((index) => {
        const code = '%u' + random(20901, 19968).toString(16)
        const radicalIndex = random(radicals.length)
        return code + signPosition[position] + radicals[radicalIndex]
      })
    }
  },
  watch: {
    checked: function (checked) {
      _.each(this.words, word => {
        word.checked = checked
      })
    },
    input: function (data) {
      this.images = []
      if (typeof data === 'string') {
        data = data.split(',')
      }
      if (_.isArray(data)) {
        this.words = _.map(_.filter(data, item => _.trim(item) != ''), word => {
          return {
            text: unescape(word),
            checked: true
          }
        })
        this.checked = data.length > 0
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
  color: white;
  background: #ff6600;
  border: none;
}
button:hover {
  background: #ff7700;
}
button:active {
  background: #ee5500;
}

header button {
  float: left;
  margin-top: 30px;
  width: auto;
  height: 24px;
  line-height: 20px;
  font-size: 16px;
  background: #41b883;
}

header button:hover {
  background: #51c893;
}
header button:active {
  background: #31a873;
}

textarea {
  width: 800px;
  height: 320px;
}
</style>
