<template>
  <div class="SingerDetails">
      <div  class="Topbg"></div>
      <layout>
        <div class="Singerbox">
                <div class="Singerboxbg">
                  <div class="center">
                        <img  class="transform-auto margin-top Singerboxbg-img" :src="SingerPic+'?param=100y100'"  alt="">
                    <!-- 名字 -->
                  <h3 class="transform-auto margin-top">{{ SingerName }}</h3>
                  <el-button type="success" icon="el-icon-check" class="transform-auto margin-top" circle></el-button>
                  <!-- 描述 -->
                  <div  class="transform-auto Singer-describes margin-top">
                    <div>
                        {{SingerDescribe}}
                    </div>
                  </div>
                  <!-- 成就 -->
                  <div  class="transform-auto">
                    <div class="transform-auto-left">
                      <div>645</div>
                      <div>单曲</div>
                    </div>
                      <div class="transform-auto-left">
                      <div>645</div>
                      <div>单曲</div>
                    </div>
                      <div class="transform-auto-left">
                      <div>645</div>
                      <div>单曲</div>
                    </div>
                      <div class="transform-auto-left">
                      <div>645</div>
                      <div>单曲</div>
                    </div>
                  </div>
                  </div>
                  
                    <div class="SingerboxdetailsF" id="frozen-btn">
                        <button class="Singerboxdetails green" @click="goToHotworks">热门作品</button>
                        <button class="Singerboxdetails purple" @click="goToallAlbum">所有专辑</button>
                        <button class="Singerboxdetails green" @click="goToallMV">相关MV</button>
                        <button class="Singerboxdetails purple" @click="goToArtistMessage">艺人介绍</button>
                    </div>
                </div>
        </div>
        
        <div class="view">
           <router-view  @getMusicMessage="showMusicMessage"></router-view>
        </div>
         
      </layout>
  </div>
</template>

<script>
import layout from '../../components/content/layout/layout.vue';
export default {
  components: { layout },
  name:'SingerDetails',
  data() {
    return {
      // 接受动态路由传来的参数
      SingerId: this.$route.params.id,
      // 歌手名称
      SingerName: "",

        // 弹出框相关变量
      dialogVisible: false,
      input:'',

      // 歌手描述
      SingerDescribe:'',
      // 歌手照片
      SingerPic:''
    };
  },
  methods: {

    showMusicMessage(val){
      this.$emit('getMusicMessage', val);
    },

    // 跳转路由 -> goto 歌手介绍
    goToArtistMessage(){
        this.$router.push("/SingerDetails/"+ this.SingerId +"/artistMessage/"+ this.SingerId);
    },
    // 跳转路由 -> goto 热门歌手
     goToHotworks(){
        this.$router.push("/SingerDetails/"+ this.SingerId +"/hotworks/"+ this.SingerId);
    },
        // 跳转路由 -> goto 所有专辑
     goToallAlbum(){
        this.$router.push("/SingerDetails/"+ this.SingerId +"/allAlbum/"+ this.SingerId);
    },
        // 跳转路由 -> goto 所有MV
     goToallMV(){
        this.$router.push("/SingerDetails/"+ this.SingerId +"/allMV/"+ this.SingerId);
    },


    // 获取歌手详情
    async gethotSingerDetails() {
      // 歌手详情
      const result = await this.$http.get("/artist/detail?id=" + this.SingerId);
      // 歌手描述
      const describeResult = await this.$http.get("/artist/desc?id=" + this.SingerId);

      this.SingerDescribe = describeResult.data.briefDesc

      console.log(result.data.data.artist);
      // 歌手名
      this.SingerName = result.data.data.artist.name;
      // 歌手照片
      this.SingerPic = result.data.data.artist.cover;

    },
  },
  // 生命周期函数  页面刷新时调用
  mounted() {
    this.gethotSingerDetails();
  },
  // 正常情况下离开组件就会执行下面这个方法，如果该组件被<keep-alive></keep-alive>包裹着，则会被缓存，不被销毁
  beforeDestroy() {
    console.log("歌手细节里的beforeDestroy");
  },
  destroyed() {
    console.log("歌手细节里的destroyed");
  },
};
</script>
<style lang="less" scoped>

// 版头大背景
.Topbg{
  width: 100%;
  height: 460px;
  background: url('~assets/img/topBg.jpg');
  background-position:center;
  // 先脱离标准流
  position: absolute;
  // 再降低层级
  z-index: -1;
}

.view {
  transform: translateY(30px);
}


.SearchAndLogin {
    position: absolute;
    font-size: 14px;
    right: 39px;
    // 居中
    top: 50%;
    transform: translate(0, -50%);
}

/* 布局样式  start*/
  .el-col {
    border-radius: 4px;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
/* 布局样式  end*/


// 歌手详情头部
.Singerboxbg {
    position: relative;
    height: 400px;
    margin-bottom: 55px;
    background-size: 100%!important;
    background-position:center!important;
    font-size: 15px;
    .Singerboxbg-img {
      width: 100px;
      height: 100px;
      border-radius: 50%;
    }
    .center {
      margin-top: 80px;
    }
    
}
.SingerboxdetailsF {
    width: 100%;
    position: absolute;
    bottom: -60px;
    display: flex;
    justify-content: center;
    .Singerboxdetails{
    width: 25%;
    height: 60px;
    text-align: center;
    }
}

.el-row {
    box-sizing: border-box;
    margin-left: 0!important;
    margin-right: 0!important;
}

 .Determinelocation {
    width: 100%;
    height: 100%;
  }

  // 歌手描述
  .Singer-describes div{
    width: 700px;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3;
    overflow:hidden;
    -webkit-box-orient: vertical;
  }

  // 居中
  .transform-auto {
        /*flex 布局*/
    display: flex;
    /*实现垂直居中*/
    align-items: center;
    /*实现水平居中*/
    justify-content: center;
    text-align: justify;
    margin:0 auto;
    color:#fff;
    // 令div变化为行内块
    .transform-auto-left {
      display: inline-block;
      margin: 30px 60px;
    }
  }

  // margin
  .margin-top {
    margin-top:10px;
  }

 #frozen-btn button {
    cursor:pointer;
    border: 0;
    text-transform: uppercase;
    font-weight: bold;
    border-radius: 50px;
    color: white;
    outline: none;
    position: relative;
    margin-top: 35px;
    height: 50px;
    width: 20%;
    margin: 0 25px;
        }

        #frozen-btn button:before {
            content: '';
            display: block;
            background: linear-gradient(to left, rgba(255, 255, 255, 0) 50%, rgba(255, 255, 255, 0.4) 50%);
            background-size: 210% 100%;
            background-position: right bottom;
            height: 100%;
            width: 100%;
            position: absolute;
            top: 0;
            bottom: 0;
            right: 0;
            left: 0;
            border-radius: 50px;
            transition: all 1s;
            -webkit-transition: all 1s;
        }

        #frozen-btn .green {
            background-image: linear-gradient(to right, #25aae1, #40e495);
            box-shadow: 0 4px 15px 0 rgba(49, 196, 190, 0.75);
        }

        #frozen-btn .purple {
            background-image: linear-gradient(to right, #6253e1, #852D91);
            box-shadow: 0 4px 15px 0 rgba(236, 116, 149, 0.75);
        }

        #frozen-btn .purple:hover:before {
            background-position: left bottom;
        }

        #frozen-btn .green:hover:before {
            background-position: left bottom;
        }
</style>
