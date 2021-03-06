<template>
  <div id="app">
    <header>
      <img class="logo" width="50" src="./assets/logo.png">
      <button @click="input = ''">Clear</button>
      <button @click="inputFakingUyghur()">Faking Uyghur</button>
      <button @click="inputFakingChinese()">Faking Chinese</button>
      <button @click="inputRealChinese()">Real Chinese</button>
    </header>
    <button @click="collect">Generate</button>
    <button @click="download">Download</button>
    <Painter v-show="images.length" @done="got" :word="currentWord"></Painter>
    <div>
      <textarea v-model="input"></textarea>
    </div>
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
      const text = zip.folder('words')
      _.each(this.images, (image, index) => {
        const fileName = (_.fill(Array(3), '0').join('') + (index + 1)).slice(-3) + '-' + image.text.slice(-1)
        console.log(fileName)
        img.file(fileName + '.png', image.url, {base64: true})
        text.file(fileName + '.txt', image.text);
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
      this.input = _.uniq(_.range(COUNT).map((index) => {
        const position = random(2) === 1 ? 'right' : 'bottom'
        const radical = _.sample(chineseRadicals[position])
        const code = this.getChinese(radical.stroke)
        const radicalCode = '%u' + radical.code
        return '%u' + code.code + signPosition[position] + radicalCode + ' - ' + (code.stroke + radical.stroke)
      }))
    },
    inputRealChinese: function () {
      const COUNT = 1000
      this.input = _.range(COUNT).map((index) => this.getChinese())
    },
    getChinese: function (stroke) {
      let radical = ['529B','53C8']
      const stroke5 = ['52A1','5305','5370','7530','53E5', '7528','767D','5C14','7ACB','5170','5361','5E73','6B63','4E3B','4E1A']
      const stroke4 = ['6C34','5E01','74E6','89C1','652F','6237','7247','8F66','6BD4','738B','4E66','5185','5C11','6C14','957F','5206','5929','4ECE','98CE','53CB']
      const stroke3 = ['4E4B','4E5F','5927','4E8E','5DE5','51E1','5343','5DDD','5BF8','5C71','624D','5915','53E3','571F','5C0F','98DE','536B','95E8','53CA','4E45']
      if (stroke < 3) {
        radical = _.concat(radical, stroke5)
      }
      if (stroke < 4) {
        radical = _.concat(radical, stroke4)
      }
      if (stroke < 5) {
        radical = _.concat(radical, stroke3)
      }

      const sampleCode = _.sample(radical)
      let strokeCount = 2
      if (_.includes(stroke5, sampleCode)) {
        strokeCount = 5
      } else if (_.includes(stroke4, sampleCode)) {
        strokeCount = 4
      } else if (_.includes(stroke3, sampleCode)) {
        strokeCount = 3
      }

      return {
        code: sampleCode,
        stroke: strokeCount
      }
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
