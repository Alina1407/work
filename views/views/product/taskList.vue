<template>
  <section>
    <p class="fanhui">
      <span @click="product()">产品</span> /
      <span @click="product()">子产品</span> /
      <span @click="product()">测试集</span> /
      <span style="color: #97a8be">案例列表</span>
    </p>
    <el-button type="primary" plain @click="returnP()">返回</el-button>
    <el-button type="primary" plain @click="addProduct">新增</el-button>
    <!-- <el-button type="primary" plain @click="">执行</el-button> -->
    <!-- 新增 -->
    <el-dialog
      title="新增"
      :visible.sync="dialogVisibleUpdate"
      width="50%"
      :modal-append-to-body='false'
      :before-close="handleClose">
          <el-form  ref="form" :model="form" label-width="120px">
            <el-form-item label="名称">
              <el-input v-model="form.name"></el-input>
            </el-form-item>
            <el-form-item label="是否SQL案例">
              <el-select width="100" v-model="form.SQLBlooe" placeholder="是否SQL案例">
                <el-option label="是" value="true"></el-option>
                <el-option label="否" value="false"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="源查询数据源">
              <template >
                <el-select v-model="form.dataSource" @change="changeData()" placeholder="请选择数据库">
                  <el-option
                    v-for="item in options"
                    :key="item.id"
                    :label="item.name"
                    :value="item.id">
                  </el-option>
                </el-select>
              </template>
            </el-form-item>
            <el-form-item label="目标查询数据源">
              <el-select v-model="form.resultSource" @change="changeData()" placeholder="目标查询数据源">
                <el-option
                  v-for="item in options"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="是否分页">
              <el-select v-model="form.usePage" placeholder="源查询数据源">
                <el-option label="是" value="true"></el-option>
                <el-option label="否" value="false"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="分页数量">
              <el-input v-model="form.pagesize"></el-input>
            </el-form-item>
            <!-- <el-form-item label="规则" v-for="(list, index) in form.rules" :key="list.key">
              <el-input v-model="list.source" style="width: 28%;"></el-input>
              <el-input v-model="list.rule" style="width: 28%;"></el-input>
              <el-input v-model="list.target" style="width: 28%;"></el-input>
                <el-button style="width: 10%" size="mini" type="danger" @click.prevent="rulesDelete(list)">删除</el-button>
            </el-form-item> -->
          <el-form-item>
            <!-- <el-button type="primary" @click="addRules()">添加规则</el-button> -->
            <el-button type="primary"  @click="onSubmit()">确认</el-button>
            <el-button @click="handleClose()">取消</el-button>
          </el-form-item>
        </el-form>
    </el-dialog>
    <!--  -->
      <el-table
        ref="multipleTable"
        :data="tableData"
        tooltip-effect="dark"
        border
        style="width: 100%;margin-top:10px;"
        @selection-change="handleSelectionChange">
        <el-table-column
          type="selection"
          width="55">
        </el-table-column>
        <el-table-column
          prop="name"
          label="名称"
          >
        </el-table-column>
        <el-table-column label="是否SQL案例">
          <template slot-scope="scope">
              <div v-html="scope.row.isSQLCase == false ? '是':'否' "></div>
          </template>
        </el-table-column>
        <el-table-column
          prop="sourceQuery.name"
          label="源查询数据源"
          >
        </el-table-column>
        <el-table-column
          prop="targetQuery.name"
          label="目标查询数据源"
          >
        </el-table-column>
        <el-table-column label="是否分页">
          <template slot-scope="scope">
              <div v-html="scope.row.usePage == false ? '是':'否' "></div>
          </template>
        </el-table-column>
        <el-table-column
          prop="pageSize"
          label="分页数量"
          >
        </el-table-column>
        <el-table-column
          label="操作"
          width="200">
          <template slot-scope="scope">
            <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
            <el-button @click="copeClick(scope.row)" type="text" size="small">复制</el-button>
            <el-button type="text" size="small" @click="handleSee(scope.row)">编辑</el-button>
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- <task id="task" :class="{active:isTask}" v-on:taskBoxShow="taskBoxShow" :taskP="taskId" :parentBoxL="productFatherID"/> -->
      <taskDetail id="isTaskDetail" :class="{active:isTaskDetail}"/>
</section>
</template>
<script type="text/javascript">
import taskDetail from "./taskDetail";
  export default {
    components: {
        taskDetail,
    },
    props:['taskListItemP'],
  data() {
            return {
              isTaskDetail:false,
              dialogStatus:'',
              taskDetailId:'',
              taskListBoxShow:false,
              dialogVisibleUpdate:false,
              testSuiteID:'',
              tableData:[],
              options:[],
              form:{
                  name:'',
                  number:'',
                  SQLBlooe:'',
                  dataSource:'',
                  resultSource:'',
                  usePage:'',
                  pagesize:'',
                  // rules:[{source:'',rule:'',target:''}]
              }

            }
        },
        methods: {
          //获取数据源
          changeData() {
            this.$axios.post('/metaData/getAllDataSource')
            .then(res => {
              this.options = res.data.result;
            })
            .catch(error => {
                console.log(error)
            })
          },
          // 案例列表
          // testCase/getTestCaseList/1/10
          tableDataList(){
            this.$axios.get('/testCase/getTestCaseList',{
              params: {
                testSuiteID:this.testSuiteID,
              }
          })
          .then(res => {
            console.log(res,"案例列表")
            this.tableData = res.data.rows
          })
          .catch(error => {
              console.log(error)
          })
          },
          handleClick(val,row) {
            this.isTaskDetail = true;
            this.taskDetailId = row.id
          },
          handleDelete(_index,obj){
            this.$axios.post('/testCase/getTestCaseList',{
              params: {
                testSuiteID:this.testSuiteID,
                id:obj.id
              }
            })
            .then(res => {
              this.tableDataList()
            })
            .catch(error => {
                console.log(error)
            })
          },
          handleClose(){
            this.dialogVisibleUpdate = false
          },
          handleSee(val){
            this.form2 = val
            // 编辑
            this.dialogVisibleUpdate = true;
          },
          handleSelectionChange(val) {
            this.multipleSelection = val;
          },
          // 新增
          addProduct(){
            this.dialogVisibleUpdate = true;
          },
          // 新增
          onSubmit() {
            this.$axios.post('/testCase/addTestCase',{
              testSuiteID:this.testSuiteID,
              name:this.form.name,
              SQLCase:this.form.SQLBlooe,
              usePage:this.form.usePage,
              pageSize:this.form.pageSize,
              sourceQueryID:this.form.dataSource,
              targetQueryID:this.form.resultSource,
            })
            .then(res => {
              this.dialogVisibleUpdate = false
              this.tableDataList()
            })
            .catch(error => {
              console.log(error)
            })
        },
        onSubmit2(){

        },
        // 判断是否展示
        product() {
        this.$emit('listenToChild',false)
        },
        //  增加案例——添加规则
        addRules(){
          this.form.rules.push({source:'',rule:'',target:''})
        },
        // 删除规则
        rulesDelete(item){
            var index = this.form.rules.indexOf(item)
            if (index !== -1) {
              this.form.rules.splice(index, 1)
            }
          },
          // 返回
        returnP(){
          this.$emit('taskListBoxShow',this.taskListBoxShow,)
        },
        // 复制
        copeClick(){

        },
        },
        mounted(){
          this.changeData()
        },
        watch: {
          taskListItemP: function(newVal,oldVal){
            this.testSuiteID = newVal
            this.tableDataList()
            // this.productFatherID = newVal.id
            // this.productChild(this.productFatherID)
          }
      },
}
</script>
<style lang="scss" scoped type="text/css">
@import "~scss_vars";
  .fanhui {
    cursor: pointer;
    a {
      text-decoration: none;
      color: #000000;
    }
  }
  #addProduct,#addBianji {
    left: 5%;
    width: 80%;
    top: 0;
    height: 380px;
    overflow-y: scroll;
  }
  #isTaskDetail {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    right: -1900px;
    background: $color-major;
    z-index: 20;
    transition: right 1s;
    -moz-transition: right 1s; /* Firefox 4 */
    -webkit-transition: right 1s; /* Safari 和 Chrome */
    -o-transition: right 1s; /* Opera */
    &.active {
      right: 0;
    }
  }
</style>
