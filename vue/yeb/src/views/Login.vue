<template>
  <div>
    <el-form :rules="rules"
             v-loading="loading"
             element-loading-text="正在登录。。。"
             element-loading-spinner="el-icon-loading"
             element-loading-background="rgba(0,0,0,0.8)"
             ref="loginForm"
             :model="loginForm"
             class="loginContainer"
             >
      <h3 class="loginTitle">登录</h3>
      <el-form-item prop="username">
        <el-input type="text" auto-complete="false" v-model="loginForm.username" placeholder="请输入用户名"></el-input>
      </el-form-item>
      <el-form-item prop="password">
        <el-input type="password" auto-complete="false" v-model="loginForm.password" placeholder="请输入密码"></el-input>
      </el-form-item>
      <el-form-item prop="code">
        <el-input @keyup.enter.native="submitLogin" type="text" auto-complete="false" v-model="loginForm.code" placeholder="点击图片更换验证码" style="width:240px;margin-right: 5px;float: left;"></el-input>
        <img :src="captchaUrl" @click="updateCaptcha"  style="padding: 0;float: left;">
      </el-form-item>

      <el-checkbox v-model="checked" class="loginRememberMe">记住我</el-checkbox>
      <el-button type="primary" style="width: 100%"  @click="submitLogin">登录</el-button>
    </el-form>

  </div>
</template>

<script>



export default {
  name: "Login",
  data(){
    return{
      captchaUrl:'/captcha?time='+new Date(),
      loginForm:{
        username:'admin',
        password:'123',
        code: ''
      },
      loading:false,
      checked:true,
      rules:{
        username:[{required:true,message:'请输入用户名',trigger:'blur'}],
        password:[{required:true,message:'请输入密码',trigger:'blur'}],
        code:[{required:true,message:'请输入验证码',trigger:'blur'}],
      }
    }

  },
  methods:{
    updateCaptcha(){
      this.captchaUrl = '/captcha?time='+new Date();
    },
    submitLogin: function () {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true;
          this.postRequest('/login', this.loginForm).then(resp => {
            if (resp) {

              this.loading = false;
              //存取用户token
              const tokenStr = resp.obj.tokenHead + resp.obj.token;
              window.sessionStorage.setItem('tokenStr', tokenStr);
              //跳转到首页
              let path = this.$route.query.redirect;
              this.$router.replace((path == '/' || path == undefined) ? '/home':path);
            }

          })
        } else {
          this.$message.error('请输入所有字段');
          return;
        }
      });
    }
  }
}
</script>

<style scoped>
  .loginContainer{
    border-radius: 15px;
    background-clip: padding-box;
    margin: 180px auto;
    width: 350px;
    padding: 15px 35px 15px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
  }

  .loginTitle{
    margin: 10px auto 30px auto;
    text-align: center;
  }

  .loginRememberMe{

    text-align: left;
    margin: 0 0 15px 0;
  }

  .el-form-item__content{
    display: flex;
    align-items: center;
  }
</style>