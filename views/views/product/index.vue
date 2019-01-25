<template>
  <section>
    <test id="test" :class="{active:isTest}" v-on:childByValue="childByValue" :parentP="parents"/>
      <el-button type="primary" @click="dialogVisible = true" style="margin-top:20px;">添加产品</el-button>
      <!-- 增加产品 -->
      <el-dialog
        title="新增"
        :visible.sync="dialogVisible"
        width="30%"
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
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addPro()">确 定</el-button>
        </span>
      </el-dialog>
      <!-- 编辑 -->
      <el-dialog
        title="修改"
        :visible.sync="dialogVisible2"
        width="30%"
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
          <el-button type="primary" @click="submite()">确 定</el-button>
        </span>
      </el-dialog>
      <!-- 产品列表 -->
      <el-table
        ref="multipleTable"
        :data="tableData"
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
            <el-button @click="handleClick(scope.row)" type="text" size="small">查看子产品</el-button>
            <el-button type="text" size="small" @click="updatePro(scope.row)">编辑</el-button>
            <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- <el-pagination
        style="margin-top:20px;"
        layout="prev, pager, next"
        :total="total"
        @current-change="current_change">
      </el-pagination> -->

  </section>
</template>


<script type="text/javascript">
import test from "./test";
import qs from 'qs'
export default {
  components: {
    test
  },
  data() {
    return {
      // pagesize:10,
      // currentPage:1,
      // total:30,
      parents:'',
      msg:'我是父组件单向传过来的数据',
      dialogVisible:false,
      dialogVisible2:false,
      isBianji: false,
      isProduct: false,
      isTest: false,
      tableData: [],
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
    // 产品列表
    product(){
      this.$axios.post('/product/getProductList/1/100',{
      })
      .then(res => {
        this.tableData = res.data.rows;
      })
      .catch(error => {
        console.log(error)
      })
    },
    // 新增产品

    addPro(){
      this.$axios.post('/product/addProduct',
        ({name:this.form.name})
      )
      .then(res => {
        this.dialogVisible = false
        this.product()
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
    submite(){
          this.$axios.post('/product/updateProduct',
            ({id:this.form2.id,name:this.form2.name})
          )
          .then(res => {
            this.dialogVisible2 = false
            this.product()
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
                ids: obj.id
              }
              })
              .then(res => {
                this.product()
              })
              .catch(error => {
                console.log(error)
              })
            })
          .catch(_ => {});
    },
    handleClick(val) {
      this.parents = val
      this.isTest = true;
    },

    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    onSubmit() {
      // 提交子产品
      this.tableData.push({
        name: this.form.name,
        father: this.form.father
      });
      // this.$refs["form"].resetFields();
    },
    childByValue(childByValue){
      this.isTest = childByValue
    },
    current_change(){

    }
  },
  mounted() {
    // toggleShow()
    this.product()
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
}
</style>
