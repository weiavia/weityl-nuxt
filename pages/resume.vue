<style lang='scss' scoped>
  .resume {
    color: #333;
    padding: 30px 0;
    box-sizing: border-box;

    
    .statement {
      margin-bottom: 20px;
      .me {
        width: 200px;
      }
      .content {
        width: calc(100% - 210px);
        box-sizing: border-box;
        padding: 0 10px;
        line-height: 1.8;
        p {
          margin-bottom: 10px;
        }
        em {
          font-weight: bold;
        }
      }
    }
    .technology {
      margin-bottom: 30px;
      text-indent: 1em;
      line-height: 1.8;
      padding: 0 1em;
    }
    .idea {
      list-style: none;
      margin-bottom: 30px;
      padding-left: 1em;
      li {
        margin-bottom: 15px;
      }
    }
    .site {
      padding-left: 1em;
      margin-bottom: 60px;
      p {
        margin-bottom: 14px;
      }
    }
  }
  .tag {
    width: 200px;
    height: 40px;
    line-height: 40px;
    background: rgba(255,255,255, 0.5);
    font-size: 20px;
    font-weight: bold;
    color: #333;
    text-indent: 1em;
    margin-bottom: 20px;
  }
  .opreation {
    text-align: center;
    font-size: 30px;
    a {
    color: #333;
    }
    
  }
</style>

<template>
  <scroll class="resume section_background">
    <div class="tag">廖威</div>
    <div class="statement clearfix">
      <img src="https://api.liaowei.info/static/images/liaowei/2.jpg" class="me fl"/>
      <div class="fr content">
        <p>99年4月一名全栈工程师，喜欢仙侠 喜欢游戏。</p>
        <p>
          第一次入门前端看的是传智播客张鹏老师的7天搞定html+css, 
          当时看了视频跟着敲一篇感觉挺简单的，再跟着李炎恢的课程做出了几个静态网站，当学到javascript时一脸懵逼什么函数作用域各种调用返回值学的乱七八糟(第一次学编程语言对这些根本没有概念),
          整天抱着《javascript高级程序设计》这本书死啃 后面找到了BLUE老师的JS视频讲的易懂又幽默（用了3个多月看了N篇终于把JS基础过了一遍），然后就会做一些小效果（选项卡轮播图之类的),
          在用了几个月的时间学了韩顺平《php从入门到精通》(讲的很幽默看着不犯困) 最后做出来了一个论坛也算是入门了WEB开发吧。
        </p>
        <p>
          读书那几年里很幸运有 <em>妙味课堂</em>(前端干货满满) <em>慕课网</em>(各种实战扩充了我的视野)  这种教育平台。  我的偶像：<a href="http://www.imooc.com/t/108955"  target="_blank" >liuyubobobo</a>。
        </p>
      </div>
      <!-- 君不见，黄河之水天上来，奔流到海不复回 君不见，高堂明镜悲白发，朝如青丝暮成雪。 人生得意须尽欢，莫使金樽空对月。天生我材必有用，千金散尽还复来。  烹羊宰牛且为乐，会须一饮三百杯。岑夫子，丹丘生，将进酒，杯莫停。与君歌一曲，请君为我倾耳听。钟鼓馔玉不足贵，但愿长醉不复醒。古来圣贤皆寂寞，惟有饮者留其名。陈王昔时宴平乐，斗酒十千恣欢谑。主人何为言少钱，径须沽取对君酌。五花马，千金裘，呼儿将出换美酒，与尔同销万古愁。 -->
    </div>

    <ul class="idea">
      <li>it这个行业每天都在学习，学的多又杂同时每天也在忘记</li>
      <li>所以就有了这个站点来记录学会的东西, 便以后工作用到某个知识点可以快速拾起。</li>
    </ul>

    <!-- <ul class="idea">
      <li>仙路尽头谁为峰、一见无始道成空</li>
      <li>魔前一叩三千年、回首凡尘不做仙</li>
      <li>我若要有天不可无我若要无天不许有</li>
      <li>牛有德在此安敢欺我？  --牛有德</li>
      <li>江湖走马，风也好，雨也罢，走的是路，交的是朋友  --牛有道</li>
      <li>你好仙侠。</li>
    </ul> -->


    <div class="tag">技术栈</div>
    <p class="technology">
      JavaScript、TypeScript、NodeJs、Php、Mysql、Linux   &nbsp; &nbsp;  类似的东西学多了 很多框架类的工具瞄一下文档都可以直接搞。
    </p>
    <div class="tag">关于本站</div>
    <div class="site">
      <p>前后端全是纯手写、前端设计是自己跟着感觉来的不会配色就用了张背景图加上透明。</p>
      <p>前端: &nbsp;&nbsp;vue(<a href="https://zh.nuxtjs.org/">Nuxt.js 开箱即用的Vue服务端渲染框架</a>) </p>
      <p>前端ui库: &nbsp;&nbsp;<a href="https://element.eleme.io/#/zh-CN">Element</a></p>
      <p>后端: &nbsp;&nbsp;Nodejs(<a href="https://nestjs.com/">Nestjs Typescript为开发语言, node版的spring</a>)</p>
      <p>数据库: &nbsp;&nbsp;Mysql</p>
      <p>服务器: &nbsp;&nbsp;Centos7.5 (腾讯云服务器)</p>
    </div>
    <p class="opreation"><a href="javascript:;" @click="onComment">给我留言</a></p>
    <reply ref="reply" :isResume="true"/>
    <comment :article="resume" @reply="onReply"/>
  </scroll>
</template>

<script>
import Reply from '@/components/reply/reply'
import Comment from '@/components/comment'
import Scroll from '@/components/scroll/scroll'
import { COMMENT_TYPE } from '@/config/enum.js'
import { GetResume } from '@/api/block'
import { nullLiteral } from '@babel/types';

export default {
  middleware: 'common',
  async asyncData({isDev, route, store, env, params, query, req, res, redirect, error}) {
    let resume = await GetResume()
    return { resume }
  },
  data () {
    return {
      resume: null
    };
  },
  mounted() {
    bus.$on('reply', (quote_id) => {
      this.onReply(quote_id)
    })
  },
  methods: {
    onComment() {
      this.$refs.reply.show(COMMENT_TYPE.ARTICLE, 1)
    },
    onReply(quote_id) {
      this.$refs.reply.show(COMMENT_TYPE.REPLY, this.resume.id, quote_id)
    },
  },
  components: {
    Scroll,
    Comment,
    Reply
  }
}
</script>