<template>
  <section>

        <div class="table">
          <!-- table -->
          <!-- <ul v-for="(list, i) in table">
            <p @click="treeshow(i)"><i class="el-icon-caret-right"></i>{{ list.name }}</p>
            <div v-show="i == navshow" v-for="(item, j) in list.product">
              <p @click="treeshow2(i+'-'+j)"><i class="el-icon-caret-right"></i>{{ item.name }}</p>
              <ul v-show="(i+'-'+j) == nav2show" v-for="(task, h) in item.child">
                <li>{{task.name}}</li>
              </ul>
            </div>
          </ul> -->
      </div>
    <!-- 工具條 -->
    <!-- <el-col :span="20"
            class="toolbar"
            style="padding-bottom: 0px;">
      <el-form :inline="true"
               :model="filters">
        <el-form-item>
          <el-button type="primary"
                     @click="handleAdd">新增</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="primary"
                     @click="del">删除</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="primary"
                     @click="isStart">启停用</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="primary"
                     @click="save">保存</el-button>
        </el-form-item>
        
      </el-form> -->
    <!-- 新增 -->
    <div style="margin:20px 0 10px;">
      <el-button type="primary" @click="dialogVisible = true,handleAdd(),resetForm('ruleForm')">新增任务</el-button>
      <el-button type="primary" @click="delSelection()">删除任务</el-button>
    </div>
    <el-dialog
      title="任务"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose">
      <!-- form start -->
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="120px" class="demo-ruleForm">
        <el-form-item label="选择产品">
          <el-select v-model="ruleForm.productId" value-key="id" placeholder="请选择产品" @change="getTestSuite(ruleForm.productId)">
            <el-option v-for="item in allProduct" :key="item.id" :label="item.name" :value="item.id"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="选择测试集">
          <el-select v-model="ruleForm.testSuiteId" value-key="id" placeholder="请选择案例集">
            <el-option v-for="item in allTestSuite" :key="item.id" :label="item.name" :value="item.id"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="激活状态">
          <el-select v-model="ruleForm.actived" value-key="index" placeholder="是否激活">
            <el-option v-for="(item,index) in activedFlag" :key="index" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="任务名称" prop="name">
          <el-input v-model="ruleForm.name"></el-input>
        </el-form-item>
        <el-form-item label="计划启动时间" prop="cron">
          <el-input v-model="ruleForm.cron"></el-input>
          <el-button id="confStyle" type="text" @click="openConf()">配置格式说明</el-button>
        </el-form-item>
        <el-form-item label="最大线程数" prop="threadNum">
          <el-input type="threadNum" v-model="ruleForm.threadNum"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button v-show="creatFormVis" type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
          <el-button v-show="eidtFormVis" type="primary" @click="eidtForm('ruleForm')">修改</el-button>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
          <el-button @click="dialogVisible = false">取 消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
    <!-- 表格名 -->
    <el-table
    ref="multipleTable"
    :data="allTestTask"
    tooltip-effect="dark"
    style="width: 100%"
    @selection-change="selsChange">
    <el-table-column
      type="selection"
      width="55">
    </el-table-column>
    <el-table-column
      prop="id"
      label="ID">
    <template slot-scope="scope">{{ scope.row.id }}</template>
    </el-table-column>
    <el-table-column
      prop="name"
      label="名称">
    </el-table-column>
    <el-table-column
      label="计划">
     <template slot-scope="scope">{{scope.row.cron}}</template>
    </el-table-column>
    <el-table-column
      prop="testSuiteName"
      label="测试集名称">
    </el-table-column>
    <el-table-column
      prop="testCaseNum"
      label="测试案例数量">
    </el-table-column>
    <el-table-column
      label="激活状态"
      width="120">
    <template slot-scope="scope">{{ scope.row.actived == true ? activedFlag[0] : activedFlag[1]}}</template>
  </el-table-column>
    <el-table-column
      prop="threadNum"
      label="最大线程数">
    </el-table-column>
    <el-table-column
      label="启动状态"
      width="120">
    <template slot-scope="scope">
      <el-button size="small" :type="scope.row.status == true ? 'success' : 'danger'"  @click="controlTask(scope.$index, scope.row)">{{ scope.row.status == true ? '启动' : '停止' }}</el-button>
    </template>
  </el-table-column>
  <el-table-column label="操作" fixed="right" width="100">
        <template slot-scope="scope">
          <el-button size="small" @click="findByTaskId(scope.$index, scope.row)">编辑</el-button>
          <!-- <el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button> -->
        </template>
      </el-table-column>
  </el-table>
  <div style="margin-top: 20px">
    <el-button @click="toggleSelection()">取消选择</el-button>
  </div>
  <!-- 删除提示框 -->
  <el-dialog title="提示" :visible.sync="delVisible" width="300px" center>
      <div class="del-dialog-cnt">删除不可恢复，是否确定删除？</div>
      <span slot="footer" class="dialog-footer">
          <el-button @click="delVisible = false">取 消</el-button>
          <el-button type="primary" @click="deleteRow" >确 定</el-button>
      </span>
  </el-dialog>
  </section>
</template>

<script>


export default {
  inject:['reload'],
  data: function () {
    return {
      allProduct:[],
      allTestSuite:[],
      allTestTask:[],
      eidtFormVis:false,
      creatFormVis:false,
      dialogVisible: false,
      testTaskId:'',
      sels:[],//选中行的数据
      ids:[],//所选id
      delVisible:false,//删除提示框状态
      //  填充条件
      tableData:[],
      startTime: '',
      endTime: '',
      multipleSelection: [],
      activedFlag:['是','否'],//测试集激活状态
      statusFlag:[1,0],
      ruleForm:{
        productId:'',
        testSuiteId:'',
        actived:'',
        name:'',
        cron:'',
        threadNum:''
      },
      rules: {
          productId: [
            { required: true, message: '请选择活动资源', trigger: 'change' }
          ],
          testSuiteId: [
            { required: true, message: '请选择活动资源', trigger: 'change' }
          ],
          actived:[
            { required: true, message: '是否激活', trigger: 'change' }
          ],
          name: [
            { required: true, message: '请输入活动名称', trigger: 'blur' },
            // { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
          cron: [
            { required: true, message: '请配置日期时间', trigger: 'change' }
          ],
          threadNum: [
            { required: true, message: '请输入最大线程数',trigger: 'blur' },
            // { type: 'number', message: '必须为数字值'}
          ],
        }

    }

  },
  methods: {
    handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
      },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        console.log(this.ruleForm);
        if (valid) {
          this.$confirm("确认提交吗？", "提示")
            .then(_ => {
              this.$axios.post('/testTask/addTestTask',{
                actived:this.ruleForm.actived == '是' ? true : false,
                // creatTime:'',
                // creatUser:'',
                cron:this.ruleForm.cron,
                // editTime:'',
                // editUser:'',
                // id:'',
                name:this.ruleForm.name,
                // status:'',
                productId:this.ruleForm.productId,
                testSuiteId:this.ruleForm.testSuiteId,
                threadNum:this.ruleForm.threadNum
              })
              .then(res => {
                this.dialogVisible = false;
                this.getTestTask();
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
    eidtForm(formName) {
      this.$refs[formName].validate((valid) => {
        console.log(this.ruleForm);
        if (valid) {
          this.$confirm("确认提交吗？", "提示")
            .then(_ => {
              this.$axios.post('/testTask/updateTestTask',{
                actived:this.ruleForm.actived == '是' ? true : false,
                // creatTime:'',
                // creatUser:'',
                cron:this.ruleForm.cron,
                // editTime:'',
                // editUser:'',
                id:this.testTaskId,
                name:this.ruleForm.name,
                // status:'',
                productId:this.ruleForm.productId,
                testSuiteId:this.ruleForm.testSuiteId,
                threadNum:this.ruleForm.threadNum
              })
              .then(res => {
                this.dialogVisible = false;
                this.getTestTask();
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
      this.$nextTick(()=>{
        this.$refs[formName].resetFields();
      })
    },
    //获取测试任务
    getTestTask() {
      this.$axios.post('/testTask/getTestTask',{
      })
      .then(res => {
        this.allTestTask = res.data.rows;
      })
    },
    //查询产品
    findAllProduct(){
      this.$axios.get('/product/getProduct',{
      })
      .then(res => {
        this.allProduct = res.data.rows;
        this.ruleForm.productId = res.data.rows[0].id;
        this.getTestSuiteIni(res.data.rows[0].id);
        console.log(this.allProduct);
      })
    },
    //初始化查询测试集
    getTestSuiteIni(productId){
      this.$axios.get('/testSuite/getTestSuiteByProId/'+productId,{
      })
      .then(res => {
        this.allTestSuite = res.data.rows;
        this.ruleForm.testSuiteId = res.data.rows[0].id;
        this.ruleForm.actived = '是';
        console.log(this.allTestSuite);
      })
    },
    //查询测试集
    getTestSuite(productId){
      this.$axios.get('/testSuite/getTestSuiteByProId/'+productId,{
      })
      .then(res => {
        this.allTestSuite = res.data.rows;
        this.ruleForm.testSuiteId = res.data.rows[0].id;
        console.log(this.allTestSuite);
      })
    },
    handleAdd(){
      this.eidtFormVis = false;//修改按钮隐藏
      this.creatFormVis = true;///创建按钮显示
    },
    //查询选中行数据
    findByTaskId(index,row){
      this.eidtFormVis = true;//修改按钮显示
      this.creatFormVis = false;///创建按钮隐藏
      this.dialogVisible = true;
      this.$axios.get('/testTask/getTestTaskById/'+row.id,{
      })
      .then(res => {
        this.testTaskId = res.data.rows.testTaskVO.id;
        this.ruleForm.name = res.data.rows.testTaskVO.name;
        this.ruleForm.cron = res.data.rows.testTaskVO.cron;
        this.ruleForm.actived = res.data.rows.testTaskVO.actived == true ? '是' : '否';
        this.ruleForm.threadNum = res.data.rows.testTaskVO.threadNum+'';
        this.ruleForm.testSuiteId = res.data.rows.testSuiteVO.id;
        this.ruleForm.productId = res.data.rows.productVO.id;
      })
    },
    //全选单选多选
    selsChange: function(sels) {
      this.sels = sels;
    },
    //删除
    delSelection:function(){
      this.delVisible = true;//显示删除弹框
      const length = this.sels.length;
      for (let i = 0; i < length; i++) {
          this.ids.push(this.sels[i].id);
      }
    },
    //确定删除数据
    deleteRow(){
      this.$axios.get("/testTask/deleteTestTask/"+this.ids,{
      }).then(res=>{
          this.$message.success('任务删除成功')
      }).catch(error=>{
          this.$message.error('任务删除失败')
      })
      this.delVisible = false;//关闭删除提示模态框
      this.reload();
    },
    //启动关闭单一定时任务
    controlTask(index,row){
      this.$axios.get('/testTask/'+(row.status == true ? 'stopTask/' : 'startTask/')+row.id,{
      })
      .then(res => {
        this.$message.success(res.data.msg)
        if(res.data.state == '1'){
          this.reload();
        }
      }).catch(error=>{
        this.$message.error(res.data.msg)
      })
    },
    openConf(){
      let routeUrl = this.$router.resolve({
          path: "/time"
     });
     window.open(routeUrl .href, '_blank');
    },
  },
  mounted() {
    this.findAllProduct();
    this.getTestTask();
  },
}
</script>

<style lang="scss">
  #confStyle{
    color: rgba(70, 68, 68, 0.5);
  }
</style>
