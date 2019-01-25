<template>
  <section>
  <p class="fanhui"><span @click="product()">产品1</span> / <span style="color: #97a8be">产品1</span></p>
      <task id="task" :class="{active:isTask}" v-on:taskBoxShow="taskBoxShow" :taskP="taskId" :parentBoxL="productFatherID"/>
      <el-button type="primary" plain @click="returnP()">返回</el-button>
      <el-button type="primary" @click="dialogVisible3 = true">添加子产品</el-button>
      <!-- 增加产品 -->
      <el-dialog
        title="新增"
        :visible.sync="dialogVisible3"
        width="30%"
        :modal-append-to-body='false'
        :before-close="handleClose">
        <el-form
          ref="form"
          :model="form"
          label-width="80px"
        >
          <el-form-item label="产品名称">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible3 = false">取 消</el-button>
          <el-button type="primary" @click="addPro()">确 定</el-button>
        </span>
      </el-dialog>
      <!-- 编辑 -->
      <el-dialog
        title="修改"
        :visible.sync="dialogVisible2"
        width="30%"
        :modal-append-to-body='false'
        :before-close="handleClose">
        <el-form
          ref="form2"
          :model="form2"
          label-width="80px"
        >
        <el-form-item label="ID">
          <el-input v-model="form2.id" :disabled="true"></el-input>
        </el-form-item>
          <el-form-item label="产品名称">
            <el-input v-model="form2.name"></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible2 = false">取 消</el-button>
          <el-button type="primary" @click="submiteUpdata()">确 定</el-button>
        </span>
      </el-dialog>
      <!-- 产品列表 -->
      <el-table
        ref="multipleTable"
        :data="productFather"
        tooltip-effect="dark"
        border
        style="margin-top:10px;"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column prop="id" label="ID"></el-table-column>
        <el-table-column prop="name" label="名称"></el-table-column>
        <el-table-column label="操作" width="220">
          <template slot-scope="scope">
            <el-button @click="handleClick(scope.$index, scope.row)" type="text" size="small">查看测试集</el-button>
            <el-button type="text" size="small" @click="updatePro(scope.row)">编辑</el-button>
            <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

  </section>
</template>


<script type="text/javascript">
import task from "./task";
import qs from 'qs'
export default {
  props:['parentP'],
  components: {
      task,
  },
  data() {
    return {
      taskId:'',
	    childValue:false,
      isTask:false,
      dialogVisible3:false,
      dialogVisible2:false,
      productFather:[],
      productFatherID:'',
      form2: {
        id:'',
        name: "",
      },
      form: {
        name: "",
        // fatherId: 2
      }
    };
  },
  methods: {
    handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
      },
    // 子产品列表
    // /product/getSubProductList/1
    productChild(){
      this.$axios.get('/product/getSubProductList',{
        params: {
          parentID:this.productFatherID
        }
      })
      .then(res => {
        this.productFather = res.data.rows
      })
      .catch(error => {
        console.log(error)
      })
    },
    // 新增产品

    addPro(){
      this.$axios.post('/product/addProduct',
        ({
          parentID:this.productFatherID,
          name:this.form.name
        })
      )
      .then(res => {
        this.dialogVisible3 = false
        this.productChild()
      })
      .catch(error => {
        console.log(error)
      })
    },
    // 编辑
    updatePro(val){
      this.dialogVisible2 = true
      this.form2.id = val.id;
      this.form2.name = val.name;
    },
    submiteUpdata(){
          this.$axios.post('/product/updateProduct',
            ({parentID:this.productFatherID,id:this.form2.id,name:this.form2.name})
          )
          .then(res => {
            console.log(res)
            this.dialogVisible2 = false
            this.productChild()
          })
          .catch(error => {
            console.log(error)
          })
      },

    // 删除产品
    handleDelete(_index, obj) {
      this.$confirm('确认删除？')
          .then(_ => {
              this.$axios.get('/product/deleteProduct',{
              params: {
                ids: obj.id,
                parentID:this.productFatherID,
              }
              })
              .then(res => {
                this.productChild()
              })
              .catch(error => {
                console.log(error)
              })
            })
          .catch(_ => {});
    },
    handleClick(val,row) {
      this.isTask = true;
      this.taskId = row.id
    },

    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    addProduct() {
      this.isProduct = true;
    },
    onSubmit() {
      // 提交子产品
      this.tableData.push({
        name: this.form.name,
        father: this.form.father
      });
      // this.$refs["form"].resetFields();
    },
    // 返回
    returnP(){
    	this.$emit('childByValue',this.childValue,)
    },
    taskBoxShow(taskBoxShow){
      this.isTask = taskBoxShow
    }
    // 判断是否展示
  },
  mounted() {
    // this.productChild()
  },
  watch: {
      parentP: function(newVal,oldVal){
        this.productFatherID = newVal.id
        this.productChild(this.productFatherID)
      }
   },
};
</script>
<style lang="scss" type="text/css">
@import "~scss_vars";
section {
  position: relative;
  width: 100%;
  // overflow: hidden;
  #addProduct {
    position: absolute;
    z-index: 14;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
    border-radius: 10px;
    background: $color-major;
    padding: 30px;
    left: 30%;
    top: 50px;
    display: none;
    &.active {
      display: inline-block;
    }
  }
  #addBianji {
    position: absolute;
    z-index: 14;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
    border-radius: 10px;
    background: $color-major;
    padding: 30px;
    left: 30%;
    top: 50px;
    display: none;
    &.active {
      display: inline-block;
    }
  }
  .el-button {
    margin-top: 0;
  }
  #test {
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
  #task {
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
}
</style>
