<!--
 * @Descripttion: 
 * @version: 
 * @Author: silencetea
 * @Date: 2020-03-14 22:53:40
 * @LastEditors: silencetea
 * @LastEditTime: 2020-03-15 15:35:52
 -->
<template>
  <div style="display: flex; justify-content: center;">
    <video
    id="my-video"
    ref="video"
    class="video-js vjs-big-play-centered"
    controls
    :preload="playerOptions.preload"
    :width="playerOptions.width"
    :height="playerOptions.height"
    :poster="playerOptions.poster"
    data-setup="{}"
    :playbackRates="playerOptions.playbackRates"
    >
      <source v-for="(item, index) in playerOptions.sources" :key="index" 
              :src="item.src" 
              :type="item.type" />
      <!-- <source src="MY_VIDEO.webm" type="video/webm" /> -->
      <p class="vjs-no-js">
      To view this video please enable JavaScript, and consider upgrading to a
      web browser that
      <a href="https://videojs.com/html5-video-support/" target="_blank"
          >supports HTML5 video</a
      >
      </p>
  </video>
  
  </div>
</template>

<script>
import 'video.js/dist/video-js.css'

import _videojs from 'video.js'
import movie from "../video/movie.ogg"
import bg from "../assets/movie.png"

const videojs = window.videojs || _videojs

export default {
  name: 'VueVideo',
  props: {
    options: {
      type: Object
    },
  },
  data() {
    return {
      player: null,
      playerOptions: {
        // videojs options
        preload: 'auto',
        width: '480',
        height: '300',
        muted: false,  // true 播放时静音
        loop:true,
        language: 'en',
        sources: [{
          type: "video/mp4",
          src: movie
        }],
        poster: bg,
        posterImage: false,
        bigPlayButton: true,
        textTrackDisplay: false,
        errorDisplay: false,
        // playbackRates: [0.5,1,1.5,2,3], // 倍速播放配置
        controlBar: {
          // remainingTimeDisplay: false,
          pictureInPicture: false,
          playToggle: {},
          progressControl: {},
          fullscreenToggle: {},
          volumeMenuButton: {
            inline: false,
            vertical: true
          },
          children: [
            {name: 'playToggle'}, // 播放按钮
            {name: 'currentTimeDisplay'}, // 当前已播放时间
            {name: 'progressControl'}, // 播放进度条
            {name: 'durationDis play'}, // 总时间
            { // 倍数播放
              name: 'playbackRateMenuButton',
              'playbackRates': [0.5, 1, 1.5, 2]
            },
            {
              name: 'volumePanel', // 音量控制
              inline: false, // 不使用水平方式
            },
            // {name: 'FullscreenToggle'} // 全屏
          ]
        }
      }
    }
  },
  methods: {
    initialize() {
      let videoOptions = Object.assign({}, this.playerOptions, this.options)
      let myVideo = videojs(this.$refs.video, videoOptions, function () {
            let newbtn1 = document.createElement('div');
            newbtn1.className = "vue-video-btns";
            newbtn1.innerHTML = `<button class="vjs-button" data-btntype="0">
    <span class="icon iconfont" data-btntype="0">&#xe82e;</span></button>
    <button class="vjs-button" data-btntype="1">
    <span class="icon iconfont" data-btntype="1">&#xe82c;</span></button>
    <button class="vjs-button" data-btntype="2">
    <span class="icon iconfont"data-btntype="2">&#xe837;</span></button>`;

            let newbtn2 = document.createElement('div');
            newbtn2.className = "vue-video-btns";
            newbtn2.innerHTML = `<button class="vjs-button" data-btntype="3">
    <span class="icon iconfont" data-btntype="3">&#xe82d;</span></button>
    <button class="vjs-button" data-btntype="4">
    <span class="icon iconfont" data-btntype="4">&#xe830;</span></button>`;

            let controlBar = document.getElementsByClassName('vjs-control-bar')[0];
            // let playControl = document.getElementsByClassName('vjs-play-control')[0]; 

            // let insertBeforeNode = document.getElementsByClassName('vjs-fullscreen-control')[0];
            let insertBeforeNode1 = document.getElementsByClassName('vjs-play-control')[0];
            let insertBeforeNode2 = insertBeforeNode1.nextElementSibling;
            // controlBar.insertBefore(newbtn,insertBeforeNode);
            controlBar.insertBefore(newbtn1,insertBeforeNode1);
            controlBar.insertBefore(newbtn2,insertBeforeNode2);
          });
	
      // myVideo.play();

      myVideo.on('loadedmetadata', () => {
        console.log('loadedmetadata-视频源数据加载完成')
        //设置上次播放时间lastLearnTime(秒)
        // myVideo.currentTime(150);

        let changeTimeBtns = document.getElementsByClassName('vue-video-btns');
        for(let i=0; i<changeTimeBtns.length; i++) {
          let changeTimeBtnitem = changeTimeBtns[i];
          changeTimeBtnitem.addEventListener("click", (e) => {
            let curTime = myVideo.currentTime();
            let btnType = e.target.getAttribute('data-btntype');
            if(btnType == 0) {
              this.playerOptions.preload = 'none';
              myVideo.pause();
            } else if(btnType == 1) {
              myVideo.currentTime(curTime - 1/12);
            } else if(btnType == 2) {
              myVideo.currentTime(curTime - 15);
            } else if(btnType == 3) {
              myVideo.currentTime(curTime + 15);
            } else if(btnType == 4) {
              myVideo.currentTime(curTime + 1/12);
            }
          })
        }
      });


      myVideo.on('timeupdate', () => {
        // console.log('timeupdate');
        this.playerOptions.preload = 'auto';
      })
      this.player = myVideo;
    },
    dispose(callback) {
      if (this.player && this.player.dispose) {
        if (this.player.techName_ !== 'Flash') {
          this.player.pause && this.player.pause()
        }
        this.player.dispose()
        this.player = null
        this.$nextTick(() => {
          this.reseted = false
          this.$nextTick(() => {
            this.reseted = true
            this.$nextTick(() => {
              callback && callback()
            })
          })
        })
      }
    }
  },

  mounted() {
    if (!this.player) { 
      this.initialize()
    }
  },
  
  beforeDestroy() {
    if (this.player) { 
      this.dispose()
    }
  },
  
}
</script>

<style>
@import url("../assets/font/iconfont.css");

.vue-video-btns {
  color: #ffffff;
  display: flex;
  align-items: center;
}
.vue-video-btns span {
  margin: 0 2px;
  padding: 0 3px;
  cursor: pointer;
  font-size: inherit;
  
}
</style>
<style scoped>

</style>