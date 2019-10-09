<template>
  <div class="login">
    <div class="login-warp">
      <div class="loginlogo">
        <img src="./logo_index.png" alt />
      </div>
      <el-form ref="form" :model="form" :rules="rules">
        <el-form-item prop="mobile">
          <el-input v-model="form.mobile" placeholder="请输入手机号"></el-input>
        </el-form-item>
        <el-form-item prop="code">
          <el-row>
            <el-col :span="14">
              <el-input v-model="form.code" placeholder="请输入验证功码"></el-input>
            </el-col>
            <el-col :span="8" :offset="2">
              <el-button
                :disabled="!!timer"
                class="colBtn"
                @click="getCode"
              >{{timer? `${codeTime}s后获取`:'获取验证码'}}</el-button>
            </el-col>
          </el-row>
        </el-form-item>
        <el-form-item prop="read">
          <el-checkbox v-model="form.read" name="type">
            我已经阅读并同意
            <a href="#">隐私条款</a>与
            <a href="#">用户协议</a>
          </el-checkbox>
        </el-form-item>
        <el-form-item>
          <el-button class="loginbtn" @click="login" :loading="loginloading" type="primary">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      form: {
        mobile: '13911111111',
        code: '246810',
        read: false
      },
      loginloading: false,
      rules: {
        mobile: [
          { required: true, message: '请输入手机号码', trigger: 'blur' },
          { min: 11, max: 11, message: '长度必须为11', trigger: 'blur' }
        ],
        code: [
          { required: true, message: '请输入验证码', trigger: 'blur' },
          { min: 6, max: 6, message: '长度必须为6', trigger: 'blur' }
        ],
        read: [
          { required: true, message: '请先阅读用户协议', trigger: 'change' },
          { pattern: /true/, message: '请先阅读用户协议', trigger: 'change' }
        ]
      },
      codeTime: 10,
      timer: null
    }
  },
  methods: {
    login () {
      this.$refs['form'].validate(valid => {
        if (valid) {
          this.submitData()
        } else {
        }
      })
    },
    submitData () {
      this.loginloading = true
      axios({
        url: 'http://ttapi.research.itcast.cn/mp/v1_0/authorizations',
        method: 'post',
        data: this.form
      })
        .then(res => {
          this.$message({
            message: '登录成功',
            type: 'success'
          })
          this.loginloading = false
          this.$router.push('/')
        })
        .catch(err => {
          console.log(err)
          this.$message.error('手机验证码错误')
        })
    },
    getCode () {
      this.$refs['form'].validateField('mobile', errMsg => {
        if (errMsg.trim().length > 0) {
          return
        }
        this.timer = setInterval(() => {
          this.codeTime--
          if (this.codeTime <= 0) {
            clearInterval(this.timer)
            this.codeTime = 10
            this.timer = null
          }
        }, 1000)
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background: url("../../assets/login_bg.jpg");
  background-size:cover;
  .login-warp {
    background: #fff;
    padding: 30px;
    min-width: 300px;
    .loginlogo {
      text-align: center;
      img {
        width: 150px;
        margin-bottom: 20px;
      }
    }
    .loginbtn {
      width: 100%;
    }
    .myitem {
      display: flex;
      justify-content: space-between;
      .itembtn {
        margin-left: 20px;
      }
    }
    .colBtn {
      width: 100%;
    }
  }
}
</style>
