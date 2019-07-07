<template>
  <div id="app">
    <div ref="list-content" style="text-align: left">
      <transition-group name="list-complete" tag="p">
        <span
          v-for="(item, index) in lists"
          v-bind:key="item + index"
          class="list-complete-item">
          {{item}}
        </span>
      </transition-group>
    </div>
    <HelloWorld />
    <div class="content-container">
      <transition @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter"
                  name="drop" :key="index" v-for="(content, index) in contents" >
        <div class="content" v-show="content.show">
         {{content.text}}
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    HelloWorld
  },
  data () {
    return {
      lists: [],
      contents: [
        {show: false, active: false},
        {show: false, active: false},
        {show: false, active: false}
      ],
      transitionContents: []
    }
  },
  mounted() {
    this.$eventHub.$on('content-add', function(target) { // 接受数据
      this.contentAdd(target)
    }.bind(this))
  },
  methods: {
    contentAdd(el) {
      if (this.transitionContents.length + 1 >= this.contents.length) {
        this.contents.push({show: false, active: false})
      }
      for (let i = 0; i < this.contents.length; i++) {
        let content = this.contents[i];
        if (!content.show) {
          content.show = true;
          content.el = el;
          content.text = el.innerText
          this.transitionContents.push(content)
          return;
        }
      }
    },
    beforeEnter(el) {
      console.log(111111111)
      this.contents.forEach((content) => {
        if (content.show) {
          if (content.active) {
            return;
          }
          content.active = true;
          let rect = content.el.getBoundingClientRect();
          let x = rect.left;
          let y = rect.top;
          el.style.display = 'block';
          el.style.webkitTransform = `translate3d(${x}px,${y}px,0)`;
          el.style.transform = `translate3d(${x}px,${y}px,0)`;
        }
      })
    },
    enter(el) {
      this.$nextTick(() => {
        let end = this.$refs['list-content'].getBoundingClientRect()
        let x = end.left;
        let y = end.top;
        el.style.webkitTransform = `translate3d(${x}px,${y}px,0)`;
        el.style.transform = `translate3d(${x}px,${y}px,0)`;
      })
    },
    afterEnter(el) {
      let content = this.transitionContents.shift();
      this.lists.unshift(content.text)
      if (content) {
        content.show = false;
        content.active = false;
        content.text = ''
        el.style.display = 'none';
      }
    },
  }
}
</script>

<style scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.content-container .content{
  position: fixed;
  left: 0px;
  top: 0;
  transition: 3s all;
}
.list-complete-item {
  transition: all 1s;
  display: inline-block;
  margin-right: 10px;
}
</style>
