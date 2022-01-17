<template>
  <div class="container">
    <div class="input-mode-icon">
      <img src="@/assets/input_text_mode.svg" v-if="isTextInput" @click="isTextInput=false"/>
      <img src="@/assets/input_voice_mode.svg" v-else @click="isTextInput=true"/>
    </div>
    <div class="input-content">
      <van-field v-if="isTextInput" v-model="text"/>
      <TouchTalk v-else/>
    </div>
    <van-icon name="smile-o" size="36px" style="margin-left: 10px;"/>
    <van-icon name="add-o" size="36px" style="margin-left: 10px;"/>
  </div>
</template>
<script>
import TouchTalk from '@/components/TouchTalk.vue'

export default {
  name: 'ChatInputBox',
  components: {
    TouchTalk
  },
  data () {
    return {
      isTextInput: true,
      text: ''
    }
  },
  methods: {
  }
}
</script>

<style lang="scss" scoped>
.container {
   display: flex;
   flex-flow: row nowrap;
   align-items: center;
   padding: 10px 5px;
   box-sizing: border-box;
}
.input-mode-icon > img {
  width: 36px;
  width: 36px;
  margin-right: 10px;
}
.input-content {
  flex-grow: 1;
}
</style>
