<style lang='scss' scoped>
  .music {
    .iconfont {
      cursor: pointer;
    }
    .word {
      margin-left: 20px;
      background-image: linear-gradient(to right, #058 0%, #666 0%);
      background-clip: text;
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      font-weight: bold;
    }
  }
</style>

<template>
  <div class="music">
    <i class="iconfont icon-bofang" v-if="!playState" @click="changePlayState(true)" title="播放"></i>
    <i class="iconfont icon-zanting" v-if="playState" @click="changePlayState(false)" title="暂停"></i>
    <i class="iconfont icon-xiayigexiayishou" title="下一首" @click="next"></i>

    <span class="word" ref="word">Music Lyric</span>
    <audio :src="song.id | songSrc" class="hide" ref="audio" />
  </div>
</template>

<script>
import axios from 'axios'
import { parseLrc } from '@/helper'
import { setTimeout, setInterval, clearInterval } from 'timers';
import songs from './song'
import song from './song';

let index = Math.floor(Math.random() * songs.length)

export default {
  data () {
    return {
      playState: false,
      lrc: null,
      song: songs[index],
      index: index
    };
  },
  async mounted() {
    this.audio = this.$refs.audio
    this.word = this.$refs.word

    this.audio.addEventListener('ended', this.ended)
    this.audio.addEventListener('timeupdate', this.lrcRun)

    let autoplay = localStorage.getItem('autoplay')
    if(autoplay === 'true' || autoplay !== 'false') {
      this.playState = true
      this.play()
    }
  },
  methods: {
    play() {
      // 加载解析歌词
      axios.get(`https://v1.itooi.cn/netease/lrc?id=${this.song.id}`).then((lrc) => {
        this.lrc = parseLrc(lrc.data).ms
        this.initLyric()

        // chrome66版本后需要用户与浏览器有交互行为后才可以自动播放 抛出异常表示不能自动播放 等用户进行点击行为后在调用 this.audio.play()
        this.audio.play().catch((err) => {
          document.addEventListener('click', this.enventPlay)
        })
      })
    },

    enventPlay() {
      this.audio.play()
      document.removeEventListener('click', this.enventPlay)
    },
    
    initLyric() {
      this.totalTime = 0
      this.currentLine = 1
      this.nextTime = this.lrc[this.currentLine].time
      this.word.innerHTML = this.lrc[this.currentLine - 1].text
      this.progress = 0
      this.progressStartTime = 0
    },
    lrcRun() {
      // 歌曲播放
      this.totalTime = this.audio.currentTime * 1000
      // 歌曲播放累积时间大于歌词出现时间
      if(this.totalTime >= this.nextTime) {
        this.currentLine ++
        // 最后一句歌词
        if(this.currentLine > this.lrc.length - 1) {
          this.word.innerHTML = this.lrc[this.lrc.length - 1].text
          this.nextTime = this.audio.duration * 1000
        } else {
          this.word.innerHTML = this.lrc[this.currentLine -1].text
          this.nextTime = this.lrc[this.currentLine].time
        }
        this.progressStartTime = this.totalTime
      }
      // 更新TEXT
      this.progress = (this.totalTime - this.progressStartTime) / (this.nextTime - this.totalTime) * 100
      this.word.style.backgroundImage = `linear-gradient(to right, #058 ${this.progress}%, #666 0%)`
    },
    jump(second) {
      this.audio.currentTime = second
      // 计算出歌词所在行
      let currentTime = second * 1000
      this.lrc.forEach((lrc, index) => {
        if(currentTime >= lrc.time && this.lrc[index + 1].time && currentTime < this.lrc[index + 1].time) {
          this.currentLine = index
          return
        }
      });
    },
    next() {
      this.index ++
      if(this.index > songs.length - 1) {
        this.index = 0
      }
      this.song = songs[this.index]
      this.play()

      this.playState = true
    },
    ended() {
      this.next()
    },
    changePlayState(boolean) {
      if(!boolean) {
        this.$refs.audio.pause()
        clearInterval(this.lrcTime)
        localStorage.setItem('autoplay', 'false')
      } else {
        // 不存在歌词表示第一次播放。否则表示暂停后播放
        if(!this.lrc) {
          this.play()
        } else {
          this.$refs.audio.play()
          this.lrcRun()
        }
        localStorage.setItem('autoplay', 'true')
      }
      this.playState = boolean
    }
  },
  filters: {
    songSrc(id) {
      return `https://v1.itooi.cn/netease/url?id=${id}&quality=flac`
    }
  }
}
</script>