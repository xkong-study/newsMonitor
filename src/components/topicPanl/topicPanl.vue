<!--消息通知面板-->
<template>
  <div>
    <!--顶部的条件搜索区-->
    <div style="margin-top:10px;margin-left: 10px;display: flex;align-items: center;">
    <span class="label" style="margin-left:0px">类型：</span>
    <el-select v-model="typed" placeholder="请选择" style="width: 100px;height:35px">
    <el-option
      v-for="item in typeOpt"
      :key="item.value"
      :label="item.label"
      :value="item.value">
    </el-option>
   </el-select>

   <span class="label">创建人：</span>
   <el-input
    placeholder="输入创建人"
    v-model="creatUser"
    clearable style="width:120px;height:35px">
  </el-input>


    <span class="label">日期范围：</span>
     <el-date-picker
      v-model="selDate"
      type="datetimerange"
      :picker-options="pickerOptions"
      range-separator="至"
      start-placeholder="开始日期"
      end-placeholder="结束日期"
      align="right" style="height: 35px;width: 230px;">
    </el-date-picker>

    <span class="label">高级搜索：</span>
      <div class="highSearch">

        <input class="custom-input" placeholder="高级搜索"/>
        <div class="edit-div" @click="searchMore"><i class="el-icon-edit"></i></div>
      </div>
       <el-button type="primary" icon="el-icon-search" class="custom-btn">搜索</el-button>
       <el-button type="primary" icon="el-icon-edit" class="custom-btn"><router-link to="/index/topicCre">创建</router-link></el-button>
    </div>

    <!--数据显示区域-->
    <div style="width:auto;height:auto;display:flex">
    <el-table :data="this.$store.getters.posts" style="width: 100%" stripe
     v-loading="loading"
    element-loading-text="拼命加载中"
    element-loading-spinner="el-icon-loading"
    element-loading-background="rgba(0, 0, 0, 0.5)">
      <el-table-column prop="id" label="ID" width="50"></el-table-column>
      <el-table-column prop="type" label="类型" width="60"></el-table-column>
      <el-table-column prop="title" label="标题" width="180"></el-table-column>
      <el-table-column prop="text" label="内容" width="280"></el-table-column>
      <el-table-column prop="userId" label="创建人" width="80"></el-table-column>
      <el-table-column prop="refPostId" label="关联话题" width="100"></el-table-column>
      <el-table-column prop="createAt" label="创建时间" width="100"></el-table-column>
      <el-table-column prop="IsUp" label="是否置顶" width="95"></el-table-column>
      <el-table-column prop="status" label="状态" width="95"></el-table-column>
      <el-table-column prop="attendCount" label="浏览量" width="95"></el-table-column>
      <el-table-column prop="likeCount" label="参与量" width="95"></el-table-column>

      <el-table-column fixed="right" label="操作" width="88" >
      <template slot-scope="scope" id="flod-column">
        <el-button @click="handleClick(scope.row)" type="text" size="small">编辑</el-button>
        <el-button type="text" size="small" @click="delPost(scope.row)">删除</el-button>
      </template>
     </el-table-column>

    </el-table>
    <div style="width:12px;height:auto;background-color:#eee;cursor:pointer" id="flod-right" @click="flod_right">
      <img src="../../assets/b.png" style="width:12px;height:12px;margin-top:195px" class="arrow-right">
    </div>
    </div>
    <!--分页-->
     <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[5, 8, 10, 15, 20]"
      :page-size="posts.length"
      layout="total, sizes, prev, pager, next, jumper"
      :total="posts.length"
      :pager-count="11">
    </el-pagination>

    <!--高级搜索对话框-->
    <el-dialog
     title="高级搜索"
    :visible.sync="dialogVisible"
    width="30%"
    :before-close="handleClose">
    <div>
       <span>标题关键字：</span>
      <el-input
          placeholder="输入标题关键字"
         v-model="titleKey"
        clearable style="width:100%">
      </el-input>
        <span>内容关键字：</span>
      <el-input
         type="textarea"
         placeholder="请输入内容"
         v-model="contentKey"
         maxlength="300"
         show-word-limit>
      </el-input>

    </div>
    <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
   </span>
</el-dialog>
  </div>
</template>

<script>
import axios from '../../axios.js'
import $ from '../../jquery-3.0.0.min.js'
export default {
  name: "topicPanl",
  data() {
    return {
      titleKey:'',
      contentKey:'',
      dialogVisible:false,
      more:'',
      typeOpt:[
     {
          value: 'post',
          label: '投稿'
    },
    {
          value: 'topic',
          label: '话题'
    }
    ],
      typed:'',
       pickerOptions: {
          shortcuts: [{
            text: '最近一周',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近一个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近三个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit('pick', [start, end]);
            }
          }]
        },
        selDate:'',
      loading:true,
      creatUser:'',
      posts:null,
      currentPage:1
    };
  },
  created(){
    // alert('钩子函数created--------------调用');
    this.init();
  },
  methods:{
    //初始化数据
    init(){
      // axios.get('/admin/user/post/list',{
      //    cursor:0,
      //    limit:1000
      // }).then(res=>{
      //   console.log('resData-------------------------',res)
      //   this.$data.resData=res;
      //     setTimeout(() => {
      //      this.$data.loading=false
      //     }, 500);
      // }).catch(err=>{
      //     console.log('topic-err----------------',err);
      // })
      console.log('vuex的post-----------',this.$store);
      this.posts=this.$store.getters.posts
      setTimeout(() => {
           this.$data.loading=false
      }, 500);
    },
    //高级匹配
    searchMore(){
      this.$data.dialogVisible=true
    },
     //点击叉号关闭对话框
     handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
     },
     //折叠效果
     flod_right(){
       console.log('------------------------sssss')
     $('.el-table__fixed-right').animate({
         width:'toggle'
     })
      if($('.arrow-right').attr('src')==='src/assets/b.png'){

         $('.arrow-right').attr('src','src/assets/a.png')
       }else{
         $('.arrow-right').attr('src','src/assets/b.png')
       }
     console.log('-------',this);
    },
    //控制分页
    handleSizeChange(pageCount){
    // console.log('--------------------------curPage',this.$data.currentPage);
    //     axios.get('/admin/user/post/list',{
    //         cursor:this.$data.currentPage-1,
    //         limit:pageCount
    //     }).then(res=>{
    //           console.log('分页后产生的数据------------------',res);
    //          this.$data.resData=res;
    //     }).catch(err=>{

    //     });
    },
    handleCurrentChange(curentPage){
      this.$data.currentPage=curentPage;
    },
    delPost(scopeRow){
      console.log('要删除的post-------',scopeRow)
      this.$store.dispatch('delPost',{id:scopeRow.id})
    }
  },

};

</script>

<style scoped>
 @import url('topicPanl.css');
</style>
