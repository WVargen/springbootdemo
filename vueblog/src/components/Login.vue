<template>
  <el-dialog class= "login_dialog" :visible.sync="visible" center :append-to-body='true' :modal-append-to-body="false" :lock-scroll="false" width="450px" @close="handleClose">
    <el-form :rules="rules" class="login-container" label-position="left"
            label-width="0px" v-loading="loading">
      <h3 class="login_title">系统登录</h3>
      <el-form-item prop="account">
        <el-input type="text" v-model="loginForm.username" auto-complete="off" placeholder="账号"></el-input>
      </el-form-item>
      <el-form-item prop="checkPass">
        <el-input type="password" v-model="loginForm.password" auto-complete="off" placeholder="密码"></el-input>
      </el-form-item>
      <el-checkbox class="login_remember" v-model="checked" label-position="left">记住密码</el-checkbox>
      <el-form-item style="width: 100%">
        <el-button type="primary" @click.native.prevent="submitClick" style="width: 100%">登录</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>
<script>
  import {postRequest} from '../utils/api'
  import {putRequest} from '../utils/api'
  export default{
    props: {
      visible: {
        type: Boolean,
        default: false
      }
    },
    data(){
      return {
        rules: {
          account: [{required: true, message: '请输入用户名', trigger: 'blur'}],
          checkPass: [{required: true, message: '请输入密码', trigger: 'blur'}]
        },
        checked: true,
        loginForm: {
          username: 'sang',
          password: '123'
        },
        loading: false
      }
    },
    name: 'LoginPopup',
    methods: {
      submitClick: function () {
        var _this = this;
        _this.loading = true;
        postRequest('/login', {
          username: this.loginForm.username,
          password: this.loginForm.password
        }).then(resp=> {
          _this.loading = false;
          if (resp.status == 200) {
            //成功
            var json = resp.data;
            if (json.status == 'success') {
              _this.$router.replace({path: '/home'});
            } else {
              _this.$alert('登录失败!', '失败!');
            }
          } else {
            //失败
            _this.$alert('登录失败!', '失败!');
          }
        }, resp=> {
          _this.loading = false;
          _this.$alert('找不到服务器⊙﹏⊙∥!', '失败!');
        });
        _this.visible = false;
      },
      handleClose() {
        var _this = this;
        //console.log("close login");
        _this.$emit('update:visible', false);
        _this.loginForm = {
          username: '',
          password: ''
        }
      }
    }
  }
</script>
<style>
  .login-container {
    border-radius: 0px;
    background-clip: padding-box;
    margin: 0px auto;
    width: 350px;
    text-align: center;
  }

  .login_title {
    margin: 0px auto 40px auto;
    text-align: center;
    color: #505458;
  }

  .login_remember {
    margin: 0px 0px 35px 0px;
    text-align: left;
  }
</style>
