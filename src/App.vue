<template>
  <div>
    <template v-for="p in docPages" :key="`pages-${p}`">
      <div>
        <template v-for="pa in getPageChilds(p)" :key="`page-${pa}`">
          <div
            :style="{
              position: 'fixed',
              textAlign: 'left',
              background: redactBlock(pa[0].Text) ? '#333' : '',
              width: getWidth(pa[0].Geometry.BoundingBox.Width),
              height: getHeight(pa[0].Geometry.BoundingBox.Height),
              left: getLeft(pa[0].Geometry.BoundingBox.Left),
              top: getTop(pa[0].Geometry.BoundingBox.Top),
            }">
            <div v-html="redactText(pa[0].Text)"></div>
          </div>
        </template>
      </div>
    </template>
  </div>
  
  
  <!-- <br /><br /><hr /><br /><br />
  {{cvDoc.Blocks}} -->
</template>

<script>
import cvDoc from '@/assets/cv.json'

export default {
  name: 'App',
  computed: {
    docPages() {
      return 1
      //return cvDoc.DocumentMetadata.Pages
    },
  },
  data() {
    return {
      cvDoc,
      windowHeight: window.innerHeight,
      windowWidth: window.innerWidth,
      personalInfo: ['PRIYANGIKA DHARANI', 'SUBRAMANIAM', 'dharanips.23@gmail.com', 'Sri Lanka', 'Nev Zealand', 'Australia'],
    }
  },
  methods: {
    onResize() {
      this.windowHeight = window.innerHeight
      this.windowWidth = window.innerWidth
    },
    getPage(i) {
      return cvDoc.Blocks.filter(b => b.BlockType === 'PAGE')[i-1]
    },
    getPageChilds(i) {
      const page = cvDoc.Blocks.filter(b => b.BlockType === 'PAGE')[i-1]
      const childs = []
      if (page.Relationships[0].Type === 'CHILD') {
        page.Relationships[0].Ids.forEach(id => {
          const child = cvDoc.Blocks.filter(b => b.Id === id)
          childs.push(child)
        });
      }
      return childs
    },
    getWidth(w) {
      return `${w * this.windowWidth}px`
    },
    getHeight(h) {
      return `${h * this.windowHeight}px`
    },
    getLeft(l) {
      return `${l * this.windowWidth}px`
    },
    getTop(t) {
      return `${t * this.windowHeight}px`
    },
    redactBlock(t) {
      let redact = false
      if (t) {
        this.personalInfo.forEach((pio) => {
          if (t === pio)
            redact = true
        })
      }
      return redact
    },
    redactText(t) {
      if (t) {
        this.personalInfo.forEach((pio) => {
          t = t.replace(pio, '<div class="redacted-text"></div>')
          console.log('> t : ', t)
        })
      }
      return t
    },
  },
  mounted() {
    this.$nextTick(() => {
      window.addEventListener('resize', this.onResize);
    })
  },

  beforeUnmount() { 
    window.removeEventListener('resize', this.onResize); 
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.redacted-text {
    background-color: #333;
    width: 5em;
    height: 1em;
    display: inline-block;
}
</style>
