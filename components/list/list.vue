<style lang='scss' scoped>
.block {
  .load_shade {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    z-index: 2;
    background: rgb(236,244,244);
    transition: all 1s;
  }

  .load_shade.normal {
    opacity: 0;
  }
  .container {
    position: relative;
    padding-bottom: 60px;
  
  }
  .list {
    position: relative;
    margin: 0 auto;
   

    .item {
      width: 303.33333px;
      position: absolute;
      transition: all 0.3s;
    }
  }
  .empty {
    font-size: 40px;
    text-align: center;
    height: calc(100vh - 50px);
    background: $color_section_background;
    padding-top: 200px;
    box-sizing: border-box;
  }
  .bottom_line {
    width: 100%;
    text-align: center;
    font-size: 14px;
    color: $color_level_1;
    padding-bottom: 10px;
    position: absolute;
    bottom: 5px;
    left: 0;
  }
}

</style>

<template>
  <scroll class="block" @onEnd="onEnd" ref="scroll">
    <div class="load_shade" ref="loadShade" />
    <div ref="wrapper" class="container">
      <div class="list" ref="content" v-show="blocks.length">
        <template v-for="(item, index) in blocks">
          <t-block :block="item"  class="item" :key="index"/>
        </template>
      </div>
      <div class="empty"  v-show="!blocks.length">空空如也</div>
      <div class="bottom_line" v-show="blocks.length">我是底线</div>
    </div>
  </scroll>
</template>

<script>
import TBlock from '@/blocks/text'
import MBlock from '@/blocks/music'
import Scroll from '@/components/scroll/scroll'
import { mapActions } from 'vuex'

export default {
  props: {
    blocks: {
      type: Array,
      defalut: []
    }
  },
  data () {
    return {
      column: 3,
      columnWidth: 303.33333,
      columnGap: 20,
      skip: 0,
      type: 0
    };
  },
  mounted() {
    this.items = document.getElementsByClassName('block_item')
    this.flag = true
    

    this.waterFall()
    this.$refs.scroll.refresh()
    this.$refs.scroll.toTop()

    window.addEventListener('resize', () => {
      if(!this.flag)
        return
      setTimeout(() => {
        this.waterFall()
      }, 300)
    })

    bus.$on('onClass', async (type) => {
      this.type = type
      this.skip = 0
      await this.getBlocks({type: this.type, skip: this.skip})
      
      if(this.$refs.scroll) {
        this.$refs.scroll.toTop()
        this.waterFall()
        this.$refs.scroll.refresh()
      }
    })
  },
  methods: {
    async onEnd() {
      await this.getBlocks({type: this.type, skip: ++ this.skip, isConcat: true})
      this.positionend()
    },
    ...mapActions(['getBlocks']),
    waterFall() {
      let windowWidth = document.body.clientWidth
      if(windowWidth <  600) { return }
    
      this.column = Math.max( Math.floor( this.$refs.wrapper.offsetWidth / this.columnWidth ), 1)
      this.$refs.content.style.width = this.columnWidth * this.column + ((this.column-1) * this.columnGap) + 'px'

      this.hState = []
      this.lastIndex = 0
      this.positionend()

      this.flag = true
      
      this.$nextTick(() => {
        let loadShade = this.$refs.loadShade
        loadShade.classList.add('normal')
        loadShade.addEventListener('transitionend', () => {
          loadShade.style.display = 'none'
        })
      })
    },
    positionend() {
      for(var i=this.lastIndex; i<this.items.length; i++) {
        let item = this.items[i]
        if(i < this.column) {
          item.style.top = 0 + 'px'
          item.style.left = i * (this.columnWidth + this.columnGap) + 'px'
          this.hState.push(item.offsetHeight)
        } else {
          let min = this.getMin()
          
          item.style.left = min.index * (this.columnWidth + this.columnGap) + 'px'
          item.style.top = min.value + 20 + 'px'
          this.hState[min.index] = min.value + item.offsetHeight + 20
        }
      }
      this.lastIndex = i

      let max = this.getMin(true)
      this.$refs.wrapper.style.height = Math.max((document.documentElement.clientHeight - 120), max.value) + 'px'
    },
    getMin(reversal = false) {
      var o = {
        index: 0,
        value: this.hState[0]
      }
      this.hState.forEach((v, i) => {
        if(v < o.value && !reversal) {
          o.index = i
          o.value = v
        } else if(v > o.value && reversal) {
          o.index = i
          o.value = v
        }
      })
      return o
    }
  },
  components:{
    TBlock,
    MBlock,
    Scroll
  }
}
</script>