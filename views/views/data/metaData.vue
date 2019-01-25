<template>
  <section>
    <el-select v-model="value" @change="changeData()" placeholder="请选择数据库" style="margin-top: 20px;">
    <el-option
      v-for="item in options"
      :key="item.id"
      :label="item.name"
      :value="item.id">
    </el-option>
  </el-select>
    <el-dialog id="mateDataInfo" title="信息展示" :visible.sync="info">
      <table style="width: 800px;">
        <tr style="background: #dfe6ec;">
          <th>名称</th>
          <th>详细</th>
          <th>数据类型</th>
          <th>长度</th>
          <th>精确值</th>
          <th>是否为空</th>
          <th>是否主键</th>
          <th>是否外键</th>
          <th>是否索引</th>
        </tr>
        <tr v-model="gridData">
          <td>{{ gridData.name }}</td>
          <td>{{ gridData.desc }}</td>
          <td>{{ gridData.dataType }}</td>
          <td>{{ gridData.dataLength }}</td>
          <td>{{ gridData.dataPrecision }}</td>
          <td>{{ gridData.isNullable }}</td>
          <td>{{ gridData.isPrimaryKey }}</td>
          <td>{{ gridData.isForeignKey }}</td>
          <td>{{ gridData.isIndex }}</td>
        </tr>
      </table>
      </el-table>
    </el-dialog>

    <el-table :data="tableID" style="width: 100%;margin-top: 20px;">
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="字段1">
              <span @click="infoDetail(props.row.name)">{{ props.row.name.name }}</span>
            </el-form-item>
            <el-form-item label="字段2">
              <span @click="infoDetail(props.row.shop)">{{ props.row.shop }}</span>
            </el-form-item>
            <el-form-item label="字段3">
              <span @click="infoDetail(props.row.id)">{{ props.row.id }}</span>
            </el-form-item>
            <el-form-item label="字段4">
              <span @click="infoDetail(props.row.shopId)">{{ props.row.shopId }}</span>
            </el-form-item>
            <el-form-item label="字段5">
              <span @click="infoDetail(props.row.category)">{{ props.row.category }}</span>
            </el-form-item>
            <el-form-item label="字段6">
              <span @click="infoDetail(props.row.address)">{{ props.row.address }}</span>
            </el-form-item>
            <el-form-item label="字段7">
              <span @click="infoDetail(props.row.desc)">{{ props.row.desc }}</span>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column label="ID" prop="id" width="100"></el-table-column>
      <el-table-column label="表名" prop="name"></el-table-column>
      <el-table-column label="版本" prop="version" width="100"></el-table-column>
    </el-table>
  </section>
</template>
<script>
export default {
  props:['metaDataP'],
  data() {
    return {
      dataMetaID:'',
      options: [],
        value: '',
      info:false,
      gridData: 
        {
          name: "",
          desc: "",
          dataType: "",
          dataLength: "",
          dataPrecision: "",
          isNullable: "",
          isPrimaryKey: "",
          isForeignKey: "",
          isIndex: "",
         
        }
      ,
      dialogTableVisible: false,
      tableID: []
    };
  },

  methods: {
    // 数据库列表
    getData(ID){
      this.$axios.post('/metaData/getAllDb',{
        dataSourceId:ID
      })
      .then(res => {
        this.options = res.data;
      })
      .catch(error => {
        console.log(error)
      })
      // http://192.168.2.103:8011/metaData/getAllDb
    },
    // 渲染数据
    changeData(){
      this.$axios.post('/metaData/getAllTable',{
        testDatabaseId:this.value
      })
      .then(res => {
        this.tableID = res.data
      })
      .catch(error => {
        console.log(error)
      })
    },
    handleChange(value) {
    },
    infoDetail(val){
      this.info = true;
      // console.log(val)
      // name: "",
          // desc: "",
          // dataType: "",
          // dataLength: "",
          // dataPrecision: "",
          // isNullable: "",
          // isPrimaryKey: "",
          // isForeignKey: "",
          // isIndex: "",
      this.gridData.name = val.name
      this.gridData.desc = val.desc
      this.gridData.dataType = val.dataType
      this.gridData.dataLength = val.dataLength
    }
  },
  mounted() {
    this.getData()
  },
 watch: {
    metaDataP: function(newVal,oldVal){
      this.dataMetaID = newVal
      console.log(this.dataMetaID)
      this.getData(this.dataMetaID)
    }
 },
  
};
</script>
<style scoped lang="scss">

.demo-table-expand {
  font-size: 0;
}
.demo-table-expand label {
  width: 90px;
  color: #99a9bf;
}
.demo-table-expand .el-form-item {
  margin-right: 0;
  margin-bottom: 0;
  width: 50%;
}
#mateDataInfo {
  .el-dialog--small {
  width: auto;
}
  tr,td,th {
  border:none;
  margin:0;
  padding: 10px;
}
}



</style>