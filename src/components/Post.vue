<template>
  <div class="post">
    <div class="post-header">
      <time @click="this.postSomething()">{{ date }}</time>
      <span>{{ authorName }}</span>
      <a :href="authorUrl">{{ authorUrl }}</a>
    </div>
    <div>
      <p v-html="data"></p>
    </div>
    <hr />
  </div>
  <hr />
</template>

<script>
export default {
  name: 'Post',
  data () {
    return {
      data: this.content
    }
  },
  props: ['postDate', 'authorName', 'authorUrl', 'content', 'contentPostTones'],

  computed: {
    date () {
      const date2 = this.postDate
      let date = date2.split(/[:,\-,T,Z]/)
      date.pop()
      const month = [
        'января',
        'февраля',
        'марта',
        'апреля',
        'мая',
        'июня',
        'июля',
        'августа',
        'сентября',
        'октября',
        'ноября',
        'декабря'
      ]
      let monthNumber = date[1].split('')
      let monthName = ''
      if (monthNumber[0] === '0') {
        monthName = month[monthNumber[1]]
      } else {
        monthName = month[monthNumber[0]]
      }

      date = `${date[3]}:${date[4]}, ${date[2]} ${monthName} ${date[0]}г`

      return date
    }
  },
  methods: {
    getColor (tone, index) {
      // const red = [255, 0, 0]
      const yellow = [255, 255, 0]
      // const green = [0, 255, 0]

      let finalColor
      if (tone >= 0) {
        let color = yellow[0] * tone
        color = 255 - color
        finalColor = [color, 255, 0, 0.6]
      } else if (tone <= 0) {
        let color = yellow[1] * -tone
        color = 255 - color
        finalColor = [255, color, 0, 0.6]
      }

      const bgc = finalColor.join(', ')

      var elem = document.getElementsByClassName(`gradient${index}`)[0]
      var style = elem.style
      style.cssText = `background-color:rgba(${bgc})`
    },
    getMarkedText (position, length, rndI, index) {
      const arr = this.data
      let flag = false
      if (length == 1) {
        flag = true
      }
      for (let char = 0; char < arr.length; char++) {
        if (char == position) {
          arr.splice(char, 1, `<span class='gradient${rndI}'>${arr[char]}`)
        } else if (char == length + position - 1 && !flag) {
          arr.splice(char, 1, `${arr[char]}</span>`)
        } else if (flag && char == length + position) {
          if (arr[char] == ' ') {
            arr.splice(char, 1, `</span>${arr[char]}`)
          } else {
            arr.splice(char, 1, `${arr[char]}</span>`)
          }
          flag = false
        }
      }

      this.data = arr

      if (this.contentPostTones.length - 1 == index) {
        this.data = arr.join('')
      }
    },
    postTone () {
      const textArr = this.content.split('')
      this.data = textArr
      let arr = this.contentPostTones
      arr.forEach((tone, index) => {
        let rndI = Math.floor(Math.random() * 100000) + 1
        this.getMarkedText(tone.position, tone.length, rndI, index, tone.tone)
        setTimeout(() => {
          this.getColor(tone.tone, rndI)
        }, 500)
      })
    }
  },
  mounted: function () {
    this.postTone()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.post {
  width: 600px;
}
p {
  white-space: pre-line;
}
.post-header {
  display: flex;
  flex-direction: column;
}
</style>
