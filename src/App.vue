<template>
  <div id="app">
    <div ref="list-content" style="text-align: left;min-height: 18px;">
      <transition-group type="transition" name="list-complete" tag="ul">
        <li
          @dragstart="dragstart"
          @dragend="dragend"
          :index="index"
          @dragover="dragover"
          draggable
          v-for="(item, index) in lists"
          :key="item + index"
          class="list-complete-item">
          {{item}}
        </li>
      </transition-group>
    </div>
    <div ref="content-copty"></div>
    <HelloWorld />
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
      lists: ['默认1', '默认2', '默认3'],
      draging: null
    }
  },
  mounted() {
    this.$eventHub.$on('content-add', function(target) { // 接受数据
      this.contentAdd(target)
    }.bind(this))
  },
  methods: {
    dragstart (e) {
      this.draging = {
        dragIndex: Number(e.target.getAttribute('index'))
      }
    },
    dragend (e) {
      if (this.draging) {
        let dragIndex = this.draging.dragIndex
        let targetIndex = this.draging.targetIndex
        let dragItem = ''
        dragItem = targetIndex && this.lists.splice(dragIndex, 1)
        targetIndex && this.lists.splice(targetIndex, 0, ...dragItem)
        this.draging = null
      }
    },
    dragover (e) {
      this.draging && (this.draging.targetIndex = (e.target.getAttribute('index')))
    },
    contentAdd(el) {
      if (!this.$refs['content-copty'] || this.$refs['content-copty'].className) {
        return
      }
      let rect = el.getBoundingClientRect();
      let x = rect.left;
      let y = rect.top;
      this.$refs['content-copty'].innerText = el.innerText
      this.$refs['content-copty'].style.left = x + 'px';
      this.$refs['content-copty'].style.top = y + 'px';
      this.$refs['content-copty'].style.display = 'block';
      this.$refs['content-copty'].className = 'content-copty'
      this.$nextTick(()=> {
        this.lists.splice(0, 0, el.innerText)
        let content = this.$refs['list-content'].getBoundingClientRect();
        let x1 = content.left;
        let y1 = content.top;
        this.$refs['content-copty'].style.left = x1 + 'px';
        this.$refs['content-copty'].style.top = y1 + 'px';
        this.$refs['content-copty'].style.opacity = 0;
        setTimeout(() => {
          this.$refs['content-copty'].innerText = ''
          this.$refs['content-copty'].style.left = '';
          this.$refs['content-copty'].style.top = '';
          this.$refs['content-copty'].style.display = 'none';
          this.$refs['content-copty'].className = ''
          this.$refs['content-copty'].style.opacity = 1;
        }, 2000)
      })
    }
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
.wrapper {
  width: 100%;
}
.content-copty{
  position: fixed;
  display: none;
  transition: 2s all;
}
.list-complete-move {
  transition: transform 2s;
}
.list-complete-item {
  display: inline-block;
  margin-right: 10px;
  transition: all 2s;
  background-color: #42b983;
}
.list-complete-enter, .list-complete-leave-to {
  opacity: 0;
}
.list-complete-leave-active {
  position: absolute;
}
.list-complete-enter, .list-complete-leave-to
  /* .list-complete-leave-active for below version 2.1.8 */ {
  opacity: 0;
}
.list-complete-leave-active {
  position: absolute;
}
</style>
