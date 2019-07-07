<template>
  <div class="hello">
    <div style="height: 100px;"></div>
    <ul>
      <li @click.stop.prevent="add">vue demo1</li>
      <li @click.stop.prevent="add">vue demo2</li>
      <li @click.stop.prevent="add">vue demo3</li>
      <li @click.stop.prevent="add">vue demo4</li>
      <li @click.stop.prevent="add">vue demo5</li>
      <li @click.stop.prevent="add">vue demo6</li>
      <li @click.stop.prevent="add">vue demo7</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
  },
  methods: {
    add(event) {
      this.$eventHub.$emit('content-add', event.target) // 发送数据
    },
  }
}
</script>

<style scoped>
  li {
    text-align: left;
  }
</style>
