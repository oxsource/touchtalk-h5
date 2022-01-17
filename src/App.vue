<template>
  <div id="app">
    <ChatInputBox class="input-box"/>
  </div>
</template>

<script>
import ChatInputBox from './components/ChatInputBox.vue'

export default {
  name: 'App',
  components: {
    ChatInputBox
  }
}
</script>

<style>
html, body {
  height: 100%;
  user-select: none;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: relative;
  height: 100%;
}
.input-box {
  position: absolute;
  bottom: 0;
  width: 100%;
}
</style>
