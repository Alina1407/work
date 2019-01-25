<template>
  <section>
    <div id="metaData" :class="{active:ismetaData}">
      <el-button @click="returnData()" style="margin-top:20px;">返回数据源</el-button>
      <metaData  :metaDataP="metaDataID"/>
    </div>
    <div id="datasource" :class="{active:isdatasource}">
    <!--工具条-->
    <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
      <el-form :inline="true" :model="filters">
        <el-form-item>
          <el-input v-model="filters.name" placeholder="请输入名称"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" v-on:click="getUsers">查询</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleAdd">新增</el-button>
        </el-form-item>
      </el-form>
    </el-col>

    <!--列表-->
    <el-table
      :data="datasource"
      highlight-current-row
      @selection-change="selsChange"
      style="width: 100%;"
    >
      <el-table-column type="selection" fixed width="55"></el-table-column>
      <el-table-column prop="id" type="index" label="id" width="50"></el-table-column>
      <el-table-column prop="name" label="名称" width="150"></el-table-column>
      <el-table-column prop="host" label="主机" width="100"></el-table-column>
      <el-table-column prop="port" label="端口" width="100"></el-table-column>
      <el-table-column prop="userName" label="用户名" width="100"></el-table-column>
      <el-table-column prop="password" label="密码" width="100"></el-table-column>
      <el-table-column prop="driver" label="驱动" width="190"></el-table-column>
      <el-table-column prop="characterSet" label="字符集" width="130"></el-table-column>
      <el-table-column prop="databaseType" label="数据库" width="130"></el-table-column>
      <el-table-column prop="databaseVersion" label="版本" width="90"></el-table-column>
      <el-table-column prop="defaultSchema" label="缺省schema" width="150"></el-table-column>
      <el-table-column prop="url" label="URL" width="300"></el-table-column>
      <el-table-column prop="editUser" label="修改者" width="130"></el-table-column>
      <el-table-column prop="editTime" label="修改时间" width="200"></el-table-column>
      <el-table-column prop="createUser" label="创建者" width="130"></el-table-column>
      <el-table-column prop="createUser" label="创建时间" width="130"></el-table-column>

      <el-table-column label="操作" fixed="right" width="350">
        <template slot-scope="scope">
          <el-button size="small" @click="synchronization(scope.$index, scope.row)">同步元数据</el-button>
          <el-button size="small" @click="seeDatasource(scope.$index, scope.row)">查看元数据</el-button>
          <el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!--工具条-->
    <el-col :span="24" class="toolbar">
      <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button>
      <el-pagination
        layout="prev, pager, next"
        @current-change="handleCurrentChange"
        :page-size="20"
        :total="total"
        style="float:right;"
      ></el-pagination>
    </el-col>

    <!--编辑界面-->
    <el-dialog
      :title="textMap[dialogStatus]"
      :visible.sync="dialogFormVisible"
       width="50%"
      :close-on-click-modal="false"
    >
    <el-form :model="creatForm" :rules="rules" ref="creatForm" label-width="100px" class="demo-ruleForm">
      <el-form-item label="名称" prop="name">
          <el-input v-model="creatForm.name" placeholder="例如：datatest数据源"></el-input>
        </el-form-item>
        <el-form-item label="主机" prop="host">
          <el-input v-model="creatForm.host" placeholder="例如：localhost"></el-input>
        </el-form-item>
        <el-form-item label="端口" prop="port">
          <el-input v-model="creatForm.port" placeholder="例如：8080"></el-input>
        </el-form-item>
        <el-form-item label="用户名" prop="userName">
          <el-input v-model="creatForm.userName" placeholder="例如：admin"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="creatForm.password" placeholder="例如：123456"></el-input>
        </el-form-item>
        <el-form-item label="驱动" prop="driver">
          <el-input v-model="creatForm.driver" placeholder="例如：com.mysql.cj.jdbc.Driver"></el-input>
        </el-form-item>
        <el-form-item label="字符集" prop="characterSet">
          <el-input v-model="creatForm.characterSet" placeholder="例如：utf-8"></el-input>
        </el-form-item>
        <el-form-item label="数据库" prop="databaseType">
          <el-select v-model="creatForm.databaseType" placeholder="请选择数据库类型">
            <el-option label="mysql" value="mysql"></el-option>
            <el-option label="oracle" value="oracle"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="版本" prop="databaseVersion">
          <el-input v-model="creatForm.databaseVersion" placeholder="例如：8"></el-input>
        </el-form-item>
        <el-form-item label="缺省schema" prop="defaultSchema">
          <el-input v-model="creatForm.defaultSchema" placeholder="例如：datatest"></el-input>
        </el-form-item>
        <el-form-item label="URL" prop="url">
          <el-input type="textarea" v-model="creatForm.url" placeholder="例如：jdbc:mysql://localhost/information_schema?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC"></el-input>
        </el-form-item>
        <el-form-item label="修改者" prop="editUser">
          <el-input v-model="creatForm.editUser" placeholder="例如：张三"></el-input>
        </el-form-item>
        <el-form-item label="创建者" prop="createUser">
          <el-input v-model="creatForm.createUser" placeholder="例如：李四"></el-input>
        </el-form-item>
      <el-form-item>
    <el-button v-show="creatFormVis" type="primary" @click="submitForm('creatForm')">创建</el-button>
    <el-button v-show="eidtFormVis" type="primary" @click="updateData('creatForm')">修改</el-button>
    <el-button @click="resetForm('creatForm')">重置</el-button>
    <el-button @click.native="dialogFormVisible=false,creatFormVis = false,eidtFormVis = false">取消</el-button>
  </el-form-item>
    </el-form>
    </el-dialog>
    </div>
  </section>
</template>

<script>
import metaData from './metaData'
export default {
  name: '',
  props: {
    // isTest:false,
  },
  components: {
      metaData,
    },
  data() {
    return {
      metaDataID:'',
      creatFormVis:false,
      eidtFormVis:false,
      ismetaData:false,//元数据层隐藏，点击查看后展示
      isdatasource:true,//数据源表单等，加载展示
      dialogStatus: "",
      textMap: {
        update: "Edit",
        create: "Create"
      },
      dialogFormVisible: false,
      filters: {
        name: ""
      },
      datasource: [],
      total: 0,
      page: 1,
      // listLoading: false,v-loading="listLoading"
      sels: [], //列表选中列

      //editFormVisible: false, //编辑界面是否显示
      //editLoading: false,
      rules: {
        name: [{ required: true, message: '请输入名称', trigger: 'blur' }],
        host:[{required:true,message:'请输入主机',trigger:'blur'}],
        port:[{required:true,message:'请输入端口',trigger:'blur'}],
        userName:[{required:true,message:'请输入用户名',trigger:'blur'}],
        password:[{required:true,message:'请输入密码',trigger:'blur'}],
        driver:[{required:true,message:'请输入驱动',trigger:'blur'}],
        characterSet:[{required:true,message:'请输入字符集 ',trigger:'blur'}],
        databaseType:[{required:true,message:'请选择数据库类型',trigger:'change'}],
        databaseVersion:[{required:true,message:'请填写版本 ',trigger:'blur'}],
        defaultSchema:[{required:true,message:'请填写Schema',trigger:'blur'}],
        url:[{required:true,message:'请填写url',trigger:'blur'}],
        editUser:[{required:true,message:'修改者',trigger:'blur'}],
        createUser:[{required:true,message:'创建者',trigger:'blur'}],
      },
      //新增数据
      creatForm: {
        id:'',
        name: "",
        host: "",
        port: "",
        userName: "",
        password: "",
        driver: "",
        characterSet: "",
        databaseType: "",
        databaseVersion: "",
        defaultSchema: "",
        url: "",
        editUser: "",
        editTime: "",
      },

      // addFormVisible: false, //新增界面是否显示
      //addLoading: false,
      addFormRules: {
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }]
      }
    };
  },
  methods: {
    // 查看元数据 元数据列表-显示
    seeDatasource(index,val){
      this.ismetaData = !this.ismetaData
      this.isdatasource = !this.isdatasource
      this.metaDataID = val.id
    },
    // 返回
    returnData(){
      this.ismetaData = !this.ismetaData
      this.isdatasource = !this.isdatasource
    },
    handleCurrentChange(val) {
      this.page = val;
      this.getUsers();
    },
    //获取数据源
    getUsers() {
      this.$axios.post('/metaData/getAllDataSource',{
        // page: this.page,
        name: this.filters.name
      })
      .then(res => {
        // this.total = res.data.total;
        this.datasource = res.data.result;
      })
    },
    //删除
    handleDel(index, row) {
      this.$confirm("确认删除该记录吗?", "提示", {
        type: "warning"
      })
        .then(() => {
          this.$axios.post('/metaData/delOneDataSource',{
            dataSourceId:row.id
          })
          .then(res => {
            this.$message({
              message: "删除成功",
              type: "success"
            });
            this.getUsers();
          })
          .catch(error => {
            this.$message({
              message: "发生了未知的错误",
              type: "warning"
            });
          })
        })
        .catch(() => {});
    },
    //显示编辑界面
    handleEdit: function(index, row) {
      this.dialogStatus = "update";
      this.eidtFormVis = true;//编辑按钮显示 
      this.creatFormVis = false;///创建按钮隐藏
      this.dialogFormVisible = true;
      this.creatForm = row
    },
    //显示新增界面
    handleAdd() {
      this.dialogStatus = "create";
      this.eidtFormVis = false;//编辑按钮隐藏
      this.creatFormVis = true;///创建按钮显示
      this.dialogFormVisible = true;

    },
    // 同步元数据 setOneMetaData
    synchronization(index,row){
      this.$axios.post('/metaData/setOneMetaData',{
        dataSourceId:row.id
      })
      .then(res => {
        this.$confirm("成功")
        // console.log(res)
      })
      .catch(error => {
        this.$confirm("同步失败")
      })
    },
    //新增
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            this.$confirm("确认提交吗？", "提示")
              .then(_ => {
                this.$axios.post('/metaData/AddOneDataSource',{
                  createTime:this.creatForm.createTime,
                  editTime:this.creatForm.editTime,
                  port:this.creatForm.port,
                  createUser:this.creatForm.createUser,
                  editUser:this.creatForm.editUser,
                  name:this.creatForm.name,
                  driver:this.creatForm.driver,
                  url:this.creatForm.url,
                  databaseType:this.creatForm.databaseType,
                  databaseVersion:this.creatForm.databaseVersion,
                  host:this.creatForm.host,
                  userName:this.creatForm.userName,
                  password:this.creatForm.password,
                  characterSet:this.creatForm.characterSet,
                  defaultSchema:this.creatForm.defaultSchema,
                })
                .then(res => {
                  this.dialogFormVisible = false
                  this.getUsers()
                })
                .catch(error => {
                  console.log(error)
                })
              })
              .catch(_ => {});
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      },
      //编辑
    updateData: function(formName) {
      this.$refs[formName].validate((valid) => {
          if (valid) {
            this.$confirm("确认修改吗？", "提示")
              .then(_ => {
                this.$axios.post('/metaData/updateOneDataSource',{
                  id:this.creatForm.id,
                  createTime:this.creatForm.createTime,
                  editTime:this.creatForm.editTime,
                  port:this.creatForm.port,
                  createUser:this.creatForm.createUser,
                  editUser:this.creatForm.editUser,
                  name:this.creatForm.name,
                  driver:this.creatForm.driver,
                  url:this.creatForm.url,
                  databaseType:this.creatForm.databaseType,
                  databaseVersion:this.creatForm.databaseVersion,
                  host:this.creatForm.host,
                  userName:this.creatForm.userName,
                  password:this.creatForm.password,
                  characterSet:this.creatForm.characterSet,
                  defaultSchema:this.creatForm.defaultSchema,
                })
                .then(res => {
                  this.dialogFormVisible = false
                  this.getUsers()
                })
                .catch(error => {
                  console.log(error)
                })
              })
              .catch(_ => {});
          } else {
            console.log('error submit!!');
            return false;
          }
        });
    },
    //全选单选多选
    selsChange: function(sels) {
      this.sels = sels;
    },

    //批量删除
    batchRemove: function() {
    //   var ids = this.sels.map(item => item.id).toString();
    //   this.$confirm("确认删除选中记录吗？", "提示", {
    //     type: "warning"
    //   })
    //     .then(() => {
    //       let para = { ids: ids };
    //       batchRemoveUser(para).then(res => {
    //         this.$message({
    //           message: "删除成功",
    //           type: "success"
    //         });
    //         this.getUsers();
    //       });
    //     })
    //     .catch(() => {});
    // }
  // },
  },
},
  mounted() {
    this.getUsers();
  },
  created() {
  }
}

</script>

<style lang="scss">
.el-table .cell, .el-table th>div {
  padding-left: 10px;
  padding-right: 10px;
}
  #metaData {
    display: none;
    &.active {
      display: inline-block;
      width: 100%;
    }
  }
  #datasource {
    display: none;
    &.active {
      width: 100%;
      display: inline-block;
    }
  }
</style>
