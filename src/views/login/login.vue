<template>
      <div class="login">
          <div style="width:600px;height:400px;display:flex;justify-content: center;flex-direction: column;align-items: center;margin-top: 60px;">
                  <img src="../../assets/admin_logo.png" style="width:500px;height:150px">
                  <div style="background-color:#FFF;width:350px;height:auto;box-shadow: 1px 0px 20px 6px #9e9e9e;border-radius: 5px;">
                        <el-input placeholder="输入用户名" v-model="username" clearable style="margin-top: 10px;width: 300px;margin-left: 25px;"></el-input>
                        <el-input placeholder="输入密码" v-model="pwd" show-password style="margin-top: 10px;width: 300px;margin-left: 25px;"></el-input>
                        <el-button type="primary" style="margin-top: 10px;width: 300px;margin-left: 25px;background-color: #01a1a1;border-color: #01a1a1;margin-bottom:10px;" @click="loginIn">登录</el-button>
                  </div>

          </div>
      </div>
</template>

<script>
import axios from '../../axios.js'
export default {
  name: 'login',
  data () {
    return {
      username:'',
      pwd:'',
      userCount:0
    }
  },

  components:{

  },
  methods:{
    loginIn(){
      if(this.$data.username==''||this.$data.pwd==''){
        this.$message.error('用户名或密码不能为空！');
        return;
      }
      console.log('获取状态管理中的username------',this.$store);
      const user=this.$store.state.admin.filter((item)=>{return this.username===item.username});
      if(user){
          sessionStorage.setItem('username',this.username);
          sessionStorage.setItem('token','Ivj6eZRx40.MTx2ZvnG8nA');
        this.$message({
          message: '欢迎'+this.$data.username+'回来！',
          type: 'success'
        });
          this.$router.push('./index')
      }else{
         if(this.$data.userCount>=5){
            console.log('登录错误次数过多');
           this.$message.error('错误次数过多，请尝试联系超级管理员！');
          }else{
          this.$message.error('用户名或密码有误！');
          this.$data.userCount++;
          }
      }
    }
  }
}

</script>

<style scoped>
    @import url('login.css');
</style>
