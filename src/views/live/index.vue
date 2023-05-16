<template>
  <div>
    <div class="container">
      <div id="video-player" class="video-player"></div>
      <div class="chat-box">
        <div class="chat-box-top">
          <i class="el-icon-chat-dot-square"></i>
          <p>互动讨论</p>
        </div>
        <div class="chat-box-center">
          <div class="message-content" v-if="messageList.length">
            <div class="message-item"
                 v-for="(item, index) in messageList"
                 :key="index"
                 :class="userInfo.userName === item.userInfo.userName ? 'self':''"
            >
              <p class="message-item-top">{{`${item.userInfo.userName}${userInfo.userName === item.userInfo.userName ? '（我）':''}${item.from === 'teacher' ? '（老师）':''}`}}</p>
              <div class="message-item-bottom">
                <div class="message-info">
                  {{item.messageInput}}
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="chat-box-bottom">
          <el-input v-model="messageInput" :maxlength="100"/>
          <el-button type="primary" class="submit-btn" @click="sendMessage">发送</el-button>
        </div>
      </div>
<!--      <el-button @click="this.toSocket"></el-button>-->
    </div>
  </div>
</template>

<script>
import HlsJsPlayer from 'xgplayer-hls.js'
import io from 'socket.io-client'
import userApi from '@/api/user'
export default {
  name: 'live',
  data () {
    return {
      videoPlayer: null,
      socket: null,
      userInfo: {},
      messageInput: '',
      messageList: []
    }
  },
  mounted () {
    userApi.getCurrentUser().then(res => {
      if (res.code === 1) {
        this.userInfo = res.response
      } else {
        this.$message.warning('发生错误')
      }
    })
    this.videoPlayer = new HlsJsPlayer({
      el: document.querySelector('#video-player'),
      url: 'http://43.139.40.29/live/livestream.m3u8',
      width: 960,
      height: 540,
      volume: 0.6, // 初始音量
      autoplay: true, // 自动播放
      playsinline: true,
      isLive: true,
      cors: true,
      lang: 'zh-cn'
    })

    this.socket = io('ws://8.130.19.2:3000')
    this.socket.on('connect', () => {
      console.log('connect！')
    })
    this.socket.on('disconnect', () => {
      console.log('disconnect!')
    })
    this.socket.on('resend', (data) => {
      console.log(data)
      this.messageList.push(data)
      this.messageInput = ''
    })
  },
  methods: {
    sendMessage () {
      this.socket.emit('send', { from: 'student', userInfo: this.userInfo, messageInput: this.messageInput })
      console.log(666)
    }
  }
}
</script>

<style scoped>
.container{
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding-top: 40px;
  overflow: hidden;
}

.chat-box{
  width: 20%;
  height: 540px;
  background: #eee;
  display: flex;
  flex-direction: column;
}

.chat-box-top{
  display: flex;
  align-items: center;
  height: 40px !important;
  padding: 0 10px;
  /*background: #b6b6b6;*/
  border-bottom: 1px solid rgba(0,0,0, .1);
}

.chat-box-top p{
  color: #2d2f33;
  font-weight: bolder;
  margin-left: 5px;
}

.el-icon-chat-dot-square{
  font-size: 24px;
}

.chat-box-center{
  flex-grow: 1;
  width: 100%;
  height: 450px;
}

.chat-box-bottom{
  height: 50px !important;
  /*border-top: 1px solid silver;*/
  display: flex;
  box-shadow: 0 2px 4px rgba(0,0,0,.5);
  align-items: center;
  padding: 0 5px;
}

.submit-btn{
  margin-left: 5px;
}

.message-content{
  height: 100%;
  padding: 8px;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}

.message-item{
  width: 100%;
  display: flex;
  flex-direction: column;
}
.message-item+.message-item{
  margin-top: 16px;
}

.message-item-top{
  font-size: 12px;
  padding: 4px 0;
  margin: 0;
}

.message-item-bottom{
  display: flex;
  align-items: center;
  justify-content: start;
}

.message-info{
  background: white;
  padding: 5px 10px;
  max-width: 80%;
  border-radius: 5px;
  font-size: 14px;
}

.self .message-item-top{
  text-align: right;
}

.self .message-item-bottom{
  justify-content: end;
}

.self .message-info{
  background: #5bcb6d;
  color: #fff;
}
</style>
