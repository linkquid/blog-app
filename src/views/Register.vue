<template>
  <div id="register" v-title data-title="注册 - xinfanBlog">
    <!--<video preload="auto" class="me-video-player" autoplay="autoplay" loop="loop">
          <source src="../../static/vedio/sea.mp4" type="video/mp4">
      </video>-->

    <div class="me-login-box me-login-box-radius">
      <h1>xinfanBlog 注册</h1>

      <el-form ref="userForm" :model="userForm" :rules="rules">
        <el-form-item prop="account">
          <el-input placeholder="账号" v-model="userForm.account"></el-input>
        </el-form-item>

        <el-form-item prop="nickname">
          <el-input placeholder="昵称" v-model="userForm.nickname"></el-input>
        </el-form-item>

        <el-form-item prop="password">
          <el-input placeholder="密码" type="password" v-model="userForm.password"></el-input>
        </el-form-item>

        <el-form-item prop="email">
          <el-input placeholder="邮箱" type="email" v-model="userForm.email"></el-input>
        </el-form-item>
        <el-button size="mini" @click="getCode">{{content}}</el-button>
        <el-form-item prop="code">
          <el-input placeholder="验证码" type="code" v-model="userForm.code"></el-input>
        </el-form-item>

        <el-form-item size="small" class="me-login-button">
          <el-button type="primary" @click.native.prevent="register('userForm')">注册</el-button>
        </el-form-item>
      </el-form>



    </div>
  </div>
</template>

<script>
  import {register} from '@/api/login'

  export default {
    name: 'Register',
    data() {
      return {
        userForm: {
          account: '',
          nickname: '',
          password: '',
          email: '',
          code: ''
        },
        content: '发送验证码',  // 按钮里显示的内容
        totalTime: 30,      //记录具体倒计时时间
        rules: {
          account: [
            {required: true, message: '请输入账号', trigger: 'blur'},
            {max: 10, message: '不能大于10个字符', trigger: 'blur'}
          ],
          nickname: [
            {required: true, message: '请输入昵称', trigger: 'blur'},
            {max: 10, message: '不能大于10个字符', trigger: 'blur'}
          ],
          password: [
            {required: true, message: '请输入密码', trigger: 'blur'},
            {max: 10, message: '不能大于10个字符', trigger: 'blur'}
          ],
          email: [
            {required: true, message: '请输入邮箱', trigger: 'blur'},
            {type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur', 'change']}
          ],
          code: [
            {required: true, message: '请输入验证码', trigger: 'blur'},
            {length: 6, message: '长度为6，请检查您的验证码是否正确', trigger: 'blur'}
          ]
        }

      }
    },
    methods: {
      countDown () {
       this.content = this.totalTime + 's'; //这里解决60秒不见了的问题
       let clock = window.setInterval(() => {
        this.totalTime--;
        this.content = this.totalTime + 's';
        if (this.totalTime < 0) {     //当倒计时小于0时清除定时器
          window.clearInterval(clock);
          this.content = '重新发送验证码';
          this.totalTime = 30;
          }
       },1000)
      },
      getCode() {
        let that = this;
        let reg = /^\w+((-\w+)|(\.\w+))*\@[A-Za-z0-9]+((\.|-)[A-Za-z0-9]+)*\.[A-Za-z0-9]+$/;
        if (!reg.test(that.userForm.email)) {
          that.$message.error("请输入正确的邮箱!");
          return false;
        }
        axios.get("/api/email?email="+that.userForm.email).then(resp=>{
          if (resp.data.code == 200) {
            that.$message.success("邮件发送成功!");
          } else {
            that.$message.error(resp.data.message);
          }
        });
        this.countDown();
      },
      register(formName) {
        let that = this
        that.$refs[formName].validate((valid) => {
          if (valid) {
            that.$store.dispatch('register', that.userForm).then(() => {
              that.$message({message: '注册成功!', type: 'success', showClose: true});
              that.$router.push({path: '/'})
            }).catch((error) => {
              if (error !== 'error') {
                that.$message({message: error, type: 'error', showClose: true});
              }
            })

          } else {
            return false;
          }
        });

      }

    }
  }
</script>

<style scoped>
  #login {
    min-width: 100%;
    min-height: 100%;
  }

  .me-video-player {
    background-color: transparent;
    width: 100%;
    height: 100%;
    object-fit: fill;
    display: block;
    position: absolute;
    left: 0;
    z-index: 0;
    top: 0;
  }

  .me-login-box {
    position: absolute;
    width: 300px;
    height: 420px;
    background-color: white;
    margin-top: 50px;
    margin-left: -180px;
    left: 50%;
    padding: 30px;
  }

  .me-login-box-radius {
    border-radius: 10px;
    box-shadow: 0px 0px 1px 1px rgba(161, 159, 159, 0.1);
  }

  .me-login-box h1 {
    text-align: center;
    font-size: 24px;
    margin-bottom: 20px;
    vertical-align: middle;
  }

  .me-login-design {
    text-align: center;
    font-family: 'Open Sans', sans-serif;
    font-size: 18px;
  }

  .me-login-design-color {
    color: #5FB878 !important;
  }

  .me-login-button {
    text-align: center;
  }

  .me-login-button button {
    width: 100%;
  }

</style>
