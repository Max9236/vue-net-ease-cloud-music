<template>
  <div id="app">

    <!-- 搜索弹框 -->
    <el-dialog 
      title="珍惜所有的不期而遇，看淡所有的不辞而别~"
      :visible.sync="dialogVisible"
      width="30%">
        <el-input prefix-icon="el-icon-search" v-model="input" 
       placeholder="请输入关键词并按下回车键" 
        @change="GotosearchDetails(input)" clearable></el-input>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>

    <!-- 返回顶部 -->
    <back-top></back-top>

        <!-- 导航栏 -->
      <nav-bar @ClickSearch="ClickSearch"></nav-bar>

      <!-- 主页面 局部刷新以下页面  keep-alive 保存状态-->
      <keep-alive exclude="search,SongDetails,rankingdetails,MVdetails,SingerDetails,user">
        <router-view  v-if="isRouterShow"/>
      </keep-alive>

    <!--music：当前播放的音乐。 list：播放列表 ：showlrc：是否显示歌词-->
       <aplayer :music="audio[0]" :showLrc="true" :autoplay="true"  id="play" class="Aplayer"/>
       <!-- 点击查看详情 -->
       <span class="My-new-iconyinle1" id="goToDetail" v-if="IsShowgoToDetail" @click="goToDetail"></span>

  <!-- 底部 -->
  <Bottom></Bottom>

  </div>

</template>

<script>
import aplayer from "vue-aplayer";  
import Bottom from '@/components/content/Bottom'
import NavBar from '@/components/content/navbar/NavBar'
import BackTop from './components/common/backTop/backTop.vue';

export default {
  components: { 
    aplayer,
    Bottom,
    NavBar,
    BackTop
  },

  //局部刷新组件要用到的方法 
  /*
    provide / inject
    就是父组件中使用provide提供变量(对象或者是一个函数),在子组件中通过inject来注入变量
    不管层级有多深，并在起上下游关系成立的时间始终生效
  */ 
  provide () {
    return {
      reload: this.reload,//把刷新方法传给子组件
    }
  },
  data() {
    return {
       dialogVisible: false,

      // 音频数据存放在次
      audio:  [{  }],
        // 局部刷新变量
      isRouterShow: true,

      input:'',

      aplayerFlag : false,

      // 当前播放的歌曲数据
      musicdata:{},
      SongId:null,

      IsShowgoToDetail:false
    }
  },
  created(){
    // 接收音乐数据
    this.$bus.$on('getMusicMessage', (val) => {
      // 上移音乐组件
    this.DisplayMusicComponent()

      //  获取得到的音乐数据 
        var data = {
            src: val.musicdata.playUrl,
            title:val.musicdata.picname,
            artist:val.musicdata.Singer,
            pic:val.musicdata.picUrl,
            lrc: val.musicdata.lyric,
        }
        // 保存音乐数据对象 和 当前播放歌曲Id
        this.musicdata = data;
        this.SongId = val.id

        // 把接收到的音乐数据添加到audio[0]中  令播放组件获取数据后进入待播放
        this.$set(this.audio,0,data);
    })
  },
  methods: {

// 上移音乐组件
DisplayMusicComponent() {
        // 显示音乐图标
      setTimeout(() => {  this.IsShowgoToDetail = true },1000)

      // 音乐组件做一次上移操作
        {
          let Aplayer = document.querySelector('.Aplayer')
          let strat = -90;
          let end = -5;
          let timer = setInterval(() => {
            if(!this.aplayerFlag){
                Aplayer.setAttribute('style','bottom:'+ ++strat +'px')
                if(strat == end ) {
                  // 上移操作只会执行一次
                  this.aplayerFlag = true;
                  // 清空定时器
                  clearInterval(timer)
                } 
            } else return 
          },1)
        }
},

  // 去歌曲详情
  goToDetail(){
    this.$router.push({name:'SongDetails',query: {id:this.SongId}})
  },
  //  搜索弹框处理函数 
  GotosearchDetails(keyword){
  // 跳转路由并把关键词传过去
    this.$router.push({name:'search',query: {key:keyword}});
  //  清空输入框内容
    this.input = '' 
  },

  // 点击搜索
  ClickSearch(){
      this.dialogVisible = true
  },

      //局部刷新组件要用到的方法 
    reload () {
      this.isRouterShow = false
      this.$nextTick(()=> {
        this.isRouterShow = true
        // Vue.nextTick()  在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。
      })
      
    },
},
};

</script>
<style  lang="less">
@import '~@/assets/css/base.less';

#goToDetail {
  font-size: 25px;
  position: fixed!important;
  bottom: 24px;
  left: 107px;
  z-index: 999;
  color: #db0e0e;
  cursor: pointer;
}

  //音乐组件
.Aplayer {
  width: 100%;
  position: fixed!important;
  bottom: -90px;
  left: -5px;
  z-index: 1;
}


// 弹出框的样式

.el-dialog {
background: url('~@/assets/img/bj88.jpg')!important;
background-size: 100%!important;
width: 920px!important;
height: 468px;
filter:(2px)!important;
}
.el-input__inner {
  height: 60px!important;
   background-color:rgba(233, 233, 233, 0.2)!important;
   color: #fff!important;
   font-size: 16px!important;
}
.el-dialog__title {
  margin: 0 auto!important;
  color: #fff!important;
}
.dialog-footer {
  display: none!important;
}

</style>
