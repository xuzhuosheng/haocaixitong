<template>

  <div id="login">

    <!--<h1 class="">耗材管理系统</h1>-->
    <div class="login">
      <el-row>
        <h1 class="">耗材管理系统</h1>
      </el-row>
      <el-row>
        <!--label-width="80px"-->
        <el-form ref="loginForm" class="st_form"  :model="login" :rules="rules" >
          <el-form-item prop="username">
            <el-input prefix-icon="el-icon-user-solid" placeholder="请输入用户名"  v-model="login.username"></el-input>
          </el-form-item>
          <el-form-item prop="pwd">
            <el-input prefix-icon="el-icon-s-cooperation" placeholder="请输入用户密码" v-model="login.pwd" type="password"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">登录</el-button>
          </el-form-item>
        </el-form>
      </el-row>
    </div>

  </div>

</template>

<script>
export default {
	    data () {
	    	return {
	    		login: {
	    			username: '',
	    			pwd: ''
	    		},
      rules: {
        username: [
          {required: true, message: '用户名不能为空', trigger: 'blur'}
        ],
        pwd: [
          {required: true, message: '密码不能为空', trigger: 'blur'}
        ]
      }
	    	}
	    },
	    created () {},
	    updated () {

	    },
	    methods: {
	    	onSubmit (data) {
      var that = this

      that.$refs.loginForm.validate((valid) => {
        if (valid) {
          var loginName = that.login.username.indexOf(' ')
          var loginPwd = that.login.pwd.indexOf(' ')
          if (loginName !== -1) {
            that.$message.error('账号有空格')
            return
          }

          if (loginPwd !== -1) {
            that.$message.error('密码有空格')
            return
          }

          // that.$httpclient.post('/login',
          that.$httpclient.loginPost('/login',
            {accountCode: that.login.username, password: that.login.pwd},
            function (res) {
              if (res.success) {
                auth.setToken(res.data.token)
                sessionStorage.setItem('username', res.data.account.userName)
                sessionStorage.setItem('accountCode', res.data.account.accountCode)
                sessionStorage.setItem('securityId', res.data.account.securityId)
                sessionStorage.setItem('id', res.data.account.id)
                that.$message.success('登录成功')
                that.$router.push({
                  path: '/'
                })
                // 判断是否为主账号，存储起来
                // if (that.login.username.indexOf('@') != -1) {
                //   sessionStorage.setItem('mianaccount', true)
                // } else {
                //   sessionStorage.setItem('mianaccount', false)
                // }
              } else {
                that.$message.error('登录失败：' + res.errorMsg)
              }
            })
        } else {
          return false
        }
      })
	    	}
	    }
}
</script>

<style scoped>
  #login{
    width: 100%;
    height: 100%;
    padding-top: 150px;
    box-sizing: border-box;
    background: url("../../static/assets/haocaiImg/sjtjfxpt_bg.png") center;
    background-size: cover;
  }
  #login h1{
    width: 100%;
    box-sizing: border-box;
    color: #006ab8;
    text-align: center;
    letter-spacing: 5px;
    font-weight: normal;
    font-size: 38px;
    /*text-shadow: -3px -3px 2px rgba(0,85,147,.8);*/
    margin-bottom: 40px;
  }
  .login{
    box-sizing: border-box;
    padding: 50px 50px 20px;
    width: 420px;
    height: 380px;
    margin: 0 auto;
    border-radius: 10px;
    background: rgba(255,255,255,1);
    box-shadow: 10px 10px 10px rgba(0,0,0,.2)
  }
  .login>div{
    width: 100%;
  }

</style>
<style>
  .st_form{
    width: 300px;
    margin: 0 auto;
  }
  .st_form .el-form-item__label{
    color: #fff;
  }
  .st_form .el-button{
    float: right;
    width: 60%;
    margin-top: 20px;
  }
  .st_form .el-input__icon{
    font-size: 20px;
    color: #aabed5;
  }
  .st_form .el-form-item.is-error .el-input__inner{
    border-color: orange;
  }
  .st_form  .el-form-item__error{
    color: orange;
  }
  .st_form .el-button--primary{
    background: #20a0ff;
  }
</style>
