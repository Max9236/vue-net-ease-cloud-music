<template>
  <div class="login">
    <!-- 登录box -->
    <div class="login_box">
      <!-- 登录logo -->
      <img src="~assets/img/logo.png" alt="" />
      <!-- 登录表单 -->
      <el-form
        class="login_form"
        ref="loginFormRef"
        :model="ruleForm"
        :rules="rules"
      >
        <!-- 用户名 -->
        <el-form-item prop="phone">
          <el-input
            placeholder="请输入网易云音乐手机号"
            prefix-icon="el-icon-user-solid"
            v-model="ruleForm.phone"
          ></el-input>
        </el-form-item>
        <!-- 密码 -->
        <el-form-item prop="password">
          <el-input
            placeholder="请输入密码"
            prefix-icon="el-icon-unlock"
            v-model="ruleForm.password"
            type="password"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" class="btn" @click="login">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      ruleForm: {
        phone: "",
        password: "",
      },
      id:'',
      rules: {
        phone: [
          { required: true, message: "请输入登录手机号", trigger: "blur" },
          { min: 11, max: 11, message: "请输入11位手机号", trigger: "blur" },
        ],
        password: [
          { required: true, message: "请输入登录密码", trigger: "blur" },
          {
            min: 6,
            max: 12,
            message: "密码长度在 6 到 12 个字符",
            trigger: "blur",
          },
        ],
      },
    };
  },
  methods: {
    login() {
      this.$refs.loginFormRef.validate(async (valid) => {
        //    表单预验证，通过则valid返回true
        if (!valid) return;
        const result = await this.$http.get("/login/cellphone?phone=" + this.ruleForm.phone + "&password=" + this.ruleForm.password);
        if (result.data.code == 502) {
          return this.$message.error("密码错误，请重新输入！");
        }
        if(result.data.code == 200){
          this.$message.success("登录成功！");
          // 保存用户id
          this.id = result.data.account.id;
          // 获取后端返回的token
          var token = result.data.token;

           //存放userTokenb
          this.$store.commit("setUserToken",token)

          //存放userid
          this.$store.commit("setUserId",this.id)

          // 保存 cookie (大部分需要登录的接口都要用到) 
          var cookie = result.data.cookie
          this.$store.commit("setCookie",cookie)
        }else return this.$message.error("登录失败");

        // 跳转到首页
        this.$router.push('/home')
        let userdata = {
          token,
          cookie,
          id:this.id
        }
        this.$bus.$emit("getUserData",userdata)


        this.$bus.$emit('getUserid', this.id);
      })    
    },
  },
};
</script>

<style lang="less" scoped>
.login {
  height: 100%;
  width: 100%;
  background: url("~assets/img/bg.jpg");
  background-size: 100% 100%;

  .login_box {
     width: 400px;
    height: 500px;
    border-radius: 10px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border: 1px solid #d3d3d3;
    box-shadow: 0px 36px 54px -12px rgb(0 0 0);

    img {
      // 居中
      width: 150px;
      position: absolute;
      left: 50%;
      transform: translate(-50%);
    }

    .login_form {
      // 居中
      margin-top: 150px;
      padding: 0 25px;

      .btn {
        width: 100%;
        height: 40px;
      }
    }
  }
}
</style>
