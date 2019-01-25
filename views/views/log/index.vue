<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true" :model="filters">
        <el-form-item >
      <el-radio-group v-model="value3"  @change="find">
      <el-radio-button label="1">轮次</el-radio-button>
      <el-radio-button label="2">案例</el-radio-button>

     </el-radio-group>
        </el-form-item>
				<el-form-item>
					<el-input v-model="filters.testRoundName" placeholder="名称"></el-input>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" v-on:click="find">查询</el-button>
				</el-form-item>

			</el-form>
		</el-col>

		<!--列表-->
		<el-table :data="tableData" highlight-current-row @selection-change="selsChange" style="width: 100%;" v-show="value3==='1'">
			<el-table-column type="selection" width="55">
			</el-table-column>
			<el-table-column type="id" width="30">
			</el-table-column>
			<el-table-column prop="suiteName" label="轮次名称" >
			</el-table-column>
      <el-table-column  label="开始时间">
      <template slot-scope="scope">{{scope.row.startTime | dateformat('YYYY-MM-DD HH:mm:ss')}}</template>
      </el-table-column>
      <el-table-column prop="endTime" label="结束时间" >
      <template slot-scope="scope">{{scope.row.endTime | dateformat('YYYY-MM-DD HH:mm:ss')}}</template>
      </el-table-column>

			<el-table-column prop="caseCount" label="案例数" >
			</el-table-column>
      <el-table-column prop="successCount" label="数据源数量" >
			</el-table-column>

			<el-table-column label="操作">
				<template slot-scope="scope">
					<el-button size="small" @click="handleEdit(scope.row)">所属案例</el-button>
				</template>
			</el-table-column>
		</el-table>
	  <el-table :data="tableData01" highlight-current-row @selection-change="selsChange" style="width: 100%;" v-show="value3==='2'">
			<el-table-column type="selection" width="55">
			</el-table-column>
			<el-table-column type="id" width="30">
			</el-table-column>
			<el-table-column prop="name" label="名称" >
			</el-table-column>
      <el-table-column prop="isSQLCase" label="是否SQL案例" :formatter="formatType">
			</el-table-column>
       <el-table-column prop="sourceQuerySource" label="源查询数据源" >
			</el-table-column>
      <el-table-column prop="targetQuerySource" label="目标查询数据源" width="200" >
			</el-table-column>

			<el-table-column prop="usePage" label="是否分页" width="100" :formatter="formatUsePage" >
			</el-table-column>
      <el-table-column prop="pageSize" label="分页数量" width="100" >
			</el-table-column>

			<el-table-column label="操作" width="250">
				<template slot-scope="scope">
					<el-button size="small" @click="handleEdit01(scope.row)">案例结果</el-button>
				</template>
			</el-table-column>
		</el-table>
		<!--工具条-->
		<el-col :span="24" class="toolbar">
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="20" :total="total" style="float:right;">
			</el-pagination>
		</el-col>

		<!--案例列表-->
    <div class="dialogFormVisible" v-show="dialogFormVisible" style="margin:10px 0;">

		  <el-table :data="tableData02" highlight-current-row @selection-change="selsChange" style="width: 100%;" >
			<el-table-column type="selection" width="35">
			</el-table-column>
			<el-table-column type="id" >
			</el-table-column>
			<el-table-column prop="name" label="名称" >
			</el-table-column>
      <el-table-column prop="isSQLCase" label="是否SQL案例" :formatter="formatType">
			</el-table-column>
       <el-table-column prop="sourceQuerySource" label="源查询数据源">
			</el-table-column>
      <el-table-column prop="targetQuerySource" label="目标查询数据源" >
			</el-table-column>
      <!-- <el-table-column width="150"  label="开始时间">
      <template slot-scope="scope">{{scope.row.startTime | dateformat('YYYY-MM-DD HH:mm:ss')}}</template>
      </el-table-column>
      <el-table-column width="150" prop="endTime" label="结束时间" >
      <template slot-scope="scope">{{scope.row.endTime | dateformat('YYYY-MM-DD HH:mm:ss')}}</template>
      </el-table-column> -->

			<el-table-column prop="usePage" label="是否分页" :formatter="formatUsePage" >
			</el-table-column>
      <el-table-column prop="pageSize" label="分页数量" >
			</el-table-column>

			<el-table-column label="操作">
				<template slot-scope="scope">
					<el-button size="small" @click="handleEdit01(scope.row)">案例结果</el-button>
				</template>
			</el-table-column>
		</el-table>
		<!--工具条-->
     <el-col :span="24" class="toolbar">
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="20" :total="total" style="float:right;">
			</el-pagination>
		</el-col>
			<div slot="footer" class="dialog-footer" style="margin-top:10px;">
			 <el-button @click.native="dialogFormVisible=false">取消</el-button>


				<!-- <el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button> -->
			</div>

    </div>
     <div class="dialogFormVisible" v-show="dialogFormVisible01" style="margin:10px 0;">
		  <el-table :data="tableData03" max-height="600" highlight-current-row @selection-change="selsChange" style="width: 100%;" >
			<el-table-column type="selection" width="55">
			</el-table-column>
      <el-table-column type="id" >
			</el-table-column>
			<el-table-column prop="targetCount" label="target数据量" >
			</el-table-column>
      <el-table-column prop="sourceCount" label="source数据量" >
			</el-table-column>
       <el-table-column width="150"  label="开始时间">
      <template slot-scope="scope">{{scope.row.startTime | dateformat('YYYY-MM-DD HH:mm:ss')}}</template>
      </el-table-column>
      <el-table-column width="150"  label="结束时间">
      <template slot-scope="scope">{{scope.row.endTime | dateformat('YYYY-MM-DD HH:mm:ss')}}</template>
      </el-table-column>
       <el-table-column prop="targetSql" label="target查询语句">
			</el-table-column>
      <el-table-column prop="sourceSql" label="source查询语句" >
			</el-table-column>
      <el-table-column prop="targetCol" label="target字段" >
			</el-table-column>
      <el-table-column prop="sourceCol" label="source字段" >
			</el-table-column>
			<el-table-column label="操作">
				<template slot-scope="scope">
					<el-button size="small" @click="handleEdit02(scope.row)">详情</el-button>
				</template>
			</el-table-column>
		</el-table>

		<!--工具条-->
     <el-col :span="24" class="toolbar">
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="20" :total="total" style="float:right;">
			</el-pagination>
		</el-col>
			<div slot="footer" class="dialog-footer" style="margin-top:10px;">
			 <el-button @click.native="dialogFormVisible01=false">取消</el-button>
			</div>

    </div>
     <div class="dialogFormVisible" v-show="dialogFormVisible02" style="margin:10px 0;">
		  <el-table :data="tableData04" max-height="600" highlight-current-row @selection-change="selsChange" style="width: 100%;" >
			<el-table-column type="selection" width="55">
			</el-table-column>
      <el-table-column type="id" >
			</el-table-column>
			<el-table-column prop="keyValue" label="keyValue" >
			</el-table-column>
      <el-table-column prop="result" label="result" >
			</el-table-column>
       <el-table-column prop="columnName" label="columnName">
			</el-table-column>
      <el-table-column prop="soruceValue" label="soruceValue" >
			</el-table-column>
      <el-table-column prop="tragetValue" label="tragetValue" >
			</el-table-column>
			<el-table-column label="操作">
				<template slot-scope="scope">
					<el-button size="small" @click="handleEdit02(scope.row)">详情</el-button>
				</template>
			</el-table-column>
		</el-table>
		<!--工具条-->
     <el-col :span="24" class="toolbar">
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="20" :total="total" style="float:right;">
			</el-pagination>
		</el-col>
			<div slot="footer" class="dialog-footer" style="margin-top:10px;">
			 <el-button @click.native="dialogFormVisible02=false">取消</el-button>
			</div>

    </div>
	</section>
</template>

<script>
import util from "../../common/js/util";
//import NProgress from 'nprogress'
import {
  getUserListPage,
  removeUser,
  batchRemoveUser,
  editUser,
  addUser,
  editUserInfo
} from "../../api/api";
import { log } from 'util';

export default {
  data() {
    return {
      dialogStatus: "",
      textMap: {
        update: "Edit",
        create: "Create"
      },
      tableData:[],
      tableData01:[],
      tableData02:[],
      tableData03:[],
      tableData04:[],
       value3: '1',
      dialogFormVisible: false,
      dialogFormVisible01:false,
      dialogFormVisible02:false,
      filters: {
        testRoundName: ""
      },
      users: [],
      total: 0,
      page: 1,
      roleAll:[],
     // listLoading: false,v-loading="listLoading"
      sels: [], //列表选中列

      //editFormVisible: false, //编辑界面是否显示
      //editLoading: false,
      editFormRules: {
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }]
      },
      //编辑界面数据
      editForm: {
        id: "0",
        name: "",
        sex: -1,
        age: 0,
        birth: "",
        addr: "",
        type:'',
        roleIds:[]
      },

      addFormVisible: false, //新增界面是否显示
      //addLoading: false,
      addFormRules: {
        name: [{ required: true, message: "请输入姓名", trigger: "blur" }]
      }
    };
  },
  methods: {
     find(){
       if(this.value3==='1'){
         this.page=1
         this.caseStatistics()
       }else{
         this.page=1
         this.TestCase()
       }
     },
     find01(){
       if(this.value3==='1'){
         this.caseStatistics()
       }else{
         this.TestCase()
       }
     },
     formatType: function(row, column) {
      return row.isSQLCase  ? "是" : "否";
    },
     formatUsePage: function(row, column) {
      return row.usePage  ? "是" : "否";
    },
     caseStatistics(){
      let para = {
        pageNum: this.page,
        pageSize:20,
        suiteName: this.filters.testRoundName
      };
      this.$axios.get('testRound/TestRound/suiteName',{params:para}
      )
      .then(res => {
        this.tableData = res.data.data.list;
       this.total = res.data.data.totalElements;
      })
      .catch(error => {
        console.log(error)
      })
      },
      TestCase(){
      let para = {
        pageNum: this.page,
        pageSize:10,
        suiteName: this.filters.testRoundName
      };
      this.$axios.get('testRound/TestCase/suiteName',{params:para}
      )
      .then(res => {
        this.tableData01 = res.data.data.list;
       this.total = res.data.data.totalElements;;
      })
      .catch(error => {
        console.log(error)
      })
      },
          //全选单选多选
    selsChange: function(sels) {
      this.sels = sels;
    },
    lockStatus: function(obj){
      var str=obj.row.status == 0 ? 1 : 0;
      obj.row.status=str;
      let para = {
        id: obj.row.id,
        status: obj.row.status
      };
      //this.listLoading = true;
      //NProgress.start();
      editUserInfo(para).then(res => {
           this.row=obj.row;
      });


    },
    handleCurrentChange(val) {
      this.page = val;
      this.find01();
    },
    switchover(){
      if(this.value3){
        this.caseStatistics()
      }else{
        this.TestCase()
      }
    },

    //显示案例列表界面
    handleEdit: function(row) {
      this.dialogFormVisible = true;
      this.$axios.get('testRound/TestCase/'+row.id,{}
      ).then(res => {
        this.tableData02 = res.data.data.list;
      //  this.total = res.data.data.totalPages;
      })
      .catch(error => {
        console.log(error)
      })
      },
      //显示案例结果
      handleEdit01: function(row) {
      let para = {
        pageNum: this.page,
        pageSize:20,
        suiteName: this.filters.testRoundName
      };
      this.dialogFormVisible01 = true;
      this.$axios.get('testRound/TestQuery/'+row.id,{}
      ).then(res => {
        this.tableData03 = res.data.data.content;
      //  this.total = res.data.data.totalPages;
      })
      .catch(error => {
        console.log(error)
      })
      },
       //显示案例结果详情
      handleEdit02: function(row) {
      let para = {
        pageNum: this.page,
        pageSize:20,
        suiteName: this.filters.testRoundName
      };
      this.dialogFormVisible02 = true;
      this.$axios.get('testRound/TestResultItem/'+row.id,{}
      ).then(res => {
        this.tableData04 = res.data.data.content;
      //  this.total = res.data.data.totalPages;
      })
      .catch(error => {
        console.log(error)
      })
      },
    //显示新增界面
    handleAdd: function() {
      this.dialogStatus = "create";
      //this.addFormVisible = true;
      this.dialogFormVisible = true;
      this.editForm = {
        id: "0",
        roleIds:[]

			}
    },


  },
  mounted() {
    this.caseStatistics();
  }
};
</script>

<style scoped>
  .dialogFormVisible {
    width: 100%;
    min-height: 800px;

    position: absolute;
    right: 0;
    z-index: 100;
    top:0;
    background: #fff;
  }
</style>
