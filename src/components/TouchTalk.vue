<template>
  <div class="touch-talk">
      <div class="touch-talk_hints" v-if="recording">
        <div class="touch-talk_hints_recording" v-if="!outside">
            <img src="@/assets/im_recording.svg"/>
            <p>松开 发送</p>
            <i class="flash"/>
        </div>
        <div class="touch-talk_hints_canceling" v-else>
            <img src="@/assets/im_canceling.svg"/>
            <p>松开 取消</p>
        </div>
    </div>
    <span
        class="touch-talk_trigger"
        @touchstart.prevent="onTouchStart"
        @touchend.prevent="onTouchEnd"
        @touchmove.prevent="onTouchMove"
      >长按发送语音</span>
  </div>
</template>
<script>
export default {
  name: 'TouchTalk',
  props: {
    timeouts: {
      type: Number,
      default: 30
    }
  },
  data () {
    return {
      outside: false,
      recording: false,
      audioChunks: [],
      audioTimeOutId: -1,
      callStopAudioRecord: () => {}
    }
  },
  methods: {
    onTouchStart () {
      this.outside = false
      this.requestAudioRecord()
    },

    onTouchEnd () {
      this.callStopAudioRecord()
    },

    onTouchMove (e) {
      const touch = e.targetTouches[0]
      if (!touch) return
      const rc = e.currentTarget.getBoundingClientRect()
      const outside = touch.clientX >= rc.right || touch.clientX <= rc.left || touch.clientY >= rc.bottom || touch.clientY <= rc.top
      if (this.outside === outside) return
      this.outside = outside
    },

    requestAudioRecord () {
      if (this.recording) return
      if (!navigator.mediaDevices) return this.$toast('设备不支持 mediaDevices')
      this.audioChunks = []
      this.audioTimeOutId = -1
      navigator.mediaDevices.getUserMedia({ audio: true }).then(stream => {
        const mediaRecorder = new MediaRecorder(stream)
        const stop = () => { this.recording && mediaRecorder.stop() }
        mediaRecorder.ondataavailable(e => this.audioChunks.push(e.data))
        mediaRecorder.onstop(() => this.finishAudioRecord(!this.outside))
        mediaRecorder.onstart(() => { this.recording = true })
        mediaRecorder.onerror(() => this.finishAudioRecord(false))
        mediaRecorder.start()
        // 录音停止和超时设置
        this.callStopAudioRecord = stop
        const ms = (this.timeouts > 0 ? this.timeouts : 30) * 1000
        this.audioTimeOutId = setTimeout(() => stop(), ms)
      }).catch(err => {
        console.error(err)
        this.$toast('录音失败')
      })
    },

    finishAudioRecord (success) {
      this.recording = false
      this.callStopAudioRecord = () => {}
      this.audioTimeOutId >= 0 && clearTimeout(this.audioTimeOutId)
      this.audioTimeOutId = -1
      const chunks = this.audioChunks || []
      this.audioChunks = []
      if (!success || this.chunks.length <= 0) return
      debugger
      var blob = new Blob(chunks, { type: 'audio/ogg; codecs=opus' })
      this.$emit('blob', blob)
    }
  }
}
</script>

<style lang="scss" scoped>
.touch-talk_hints {
    position: fixed;
    box-sizing: border-box;
    left: 25vw;
    top: calc(50vh - 50vw);
    width: 50vw;
    height: 50vw;
    background: rgba(51, 51, 51, 0.349);
    border-radius: 15px;
    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
    align-items: center;
    padding: 20px;
    z-index: 999;
}
.touch-talk_hints_recording, .touch-talk_hints_canceling {
    box-sizing: border-box;
    width: 100%;
    height: 100%;
    display: flex;
    flex-flow: column nowrap;
    justify-content: center;
    align-items: center;

    & > p {
        font-size: 16px;
        color: #FFF;
        padding: 2px 10px;
    }

    & > img {
        width: 50%;
        box-sizing: border-box;
    }
}
.touch-talk_hints_canceling {
    & > img {
        position: relative;
        left: -2px;
        padding: 10px;
    }

    & > p {
        background: rgb(230, 49, 49);
        border-radius: 5px;
    }
}
.touch-talk_hints_recording .flash{
  width: 10px;
  height: 10px;
  background: #F00;
  border-radius: 50%;
  animation: flash 1.5s infinite;
}
@keyframes flash {
  0% { opacity: 1.0;}
  45% {opacity: 0.3;}
  100% {opacity: 1.0;}
}

.touch-talk_trigger {
    display: inline-block;
    color: #333;
    user-select: none;
    -moz-user-select: -moz-none;
    -webkit-user-select: none;
    padding: 10px 0px;
    width: 100%;
    background: rgba(0, 0, 0, 0.05);
    border-radius: 5px;
}
</style>
