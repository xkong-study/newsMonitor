<!--消息通知面板-->
<template>
  <div style="width:80%;min-height:550px;height:auto;margin:0 auto">
  <div class="input-div"><span class="label">昵称：</span><el-input placeholder="请输入昵称" v-model="nickname" clearable class="full"></el-input></div>
   <div class="input-div"><span class="label">头像：</span><el-input placeholder="请输入URL" v-model="avatar" clearable class="full"></el-input></div>
   <div class="input-div">
    <el-button type="info" @click="reset">重置</el-button>
     <el-button type="primary" @click="creUser">创建</el-button>
   </div>
  </div>
</template>

<script>

///admin/user/create

import axios from '../../axios.js'
export default {
  name: 'usercRePanl',
  data () {
    return {
        nickname: '',
        avatar:''
    }
  },
   methods: {
       creUser() {
        // axios.post('/admin/user/create',{
        //     nickname:this.$data.nickname,
        //     avatar:this.$data.avatar
        // }).then(res=>{
        //   this.$data.nickname='',
        //   this.$data.avatar=''
        //   console.log('创建用户-----------------',res);
        //    if(res.data.id){
        //       this.$notify({
        //       title: '成功',
        //       message: '成功创建一个用户',
        //       type: 'success'
        //      });
        //    }
        // }).catch(err=>{
        //       this.$notify.error({
        //       title: '错误',
        //       message: '创建用户失败！'
        //      });
        // });
        if(this.nickname&&this.avatar){
          const users=this.$store.getters.users
        const lastId=users[users.length-1].id
        const data={
          id:lastId+1,
          nickname:this.nickname,
          regInfo:'wxapp',
          createAt:new Date().toLocaleDateString(),
          updateAt:new Date().toLocaleDateString()
        };
        this.$store.dispatch('addUser',data);
        this.$data.nickname='';
        this.$data.avatar='';
        this.$notify({
          title: '创建',
          message: '创建用户成功！！！',
          type: 'success'
        });
        }else{
           this.$notify({
          title: '警告',
          message: '用户名和头像不能为空！！！',
          type: 'warning'
        });
        }
      
      },
        reset() {
        this.$data.nickname='',
        this.$data.avatar=''
        this.$notify.info({
          title: '消息',
          message: '已清空一个正在创建中的用户'
        });
      }

    }
}
</script>

<style scoped>
 /*scoped代表组件的私有样式（防止样式污染） 不包括import的css文件*/
  .full{
    width:100%
  }
  @import url('usercRePanl.css');
</style>
