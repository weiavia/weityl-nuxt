<style lang='scss' scoped>
  .container {
    height: 100vh;
    width: 100%;
    max-width: 1300px;
    // min-width: 660px;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    position: relative;
    padding: 0 15px;
    box-sizing: border-box;
    .main {
      position: relative;
      width: calc(100% - 320px);
      height: 100%;
      flex-grow: 1;
      .topbar {
        width: 100%;
        margin-bottom: 20px;
      }
      .page {
        width: 100%;
        height: calc(100% - 90px);
        margin-bottom: 10px;
      }
      footer {
        height: 25px;
        line-height: 25px;
        background: $color_section_background;
        font-size: 12px;
        padding: 0 10px;
        a {
          color: #333;
          text-decoration: none;
        }
      }
    }
    

    .slidebar {
      width: 300px;
      height: 100%;
      margin-right: 20px;
    }
  }

</style>

<template>
  <div>
    <div class="background" />
    <div class="container">
      <sidebar class="slidebar" v-show="sidebarShow"/>
      <div class="main">
        <topbar class="topbar"/>
        <Nuxt class="page" />
        <footer><a href="http://www.beian.miit.gov.cn" target="_blank">Copyright © 2019 路过一只大侠 &nbsp; 湘ICP备19010169号</a> </footer>
        <!-- <Nuxt class="page" keepAlive /> -->
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from '@/layouts/sidebar/index'
import Topbar from '@/layouts/topbar/index'
import { mapGetters, mapMutations } from 'vuex';
export default {
  data() {
    return {

    }
  },
  mounted() {
    window.vm = this
  },
  methods: {
    ...mapMutations(['setSidebarShow'])
  },
  computed:  {
    ...mapGetters(['sidebarShow']),
  },
  components: {
    Sidebar,
    Topbar
  }
}
</script>