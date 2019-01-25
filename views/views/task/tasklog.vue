<template>
  <section>
    <el-table ref="multipleTable"
              :data="tableData3"
              tooltip-effect="dark"
              style="width: 100%"
              @selection-change="handleSelectionChange">
      <el-table-column type="selection"
                       width="55">
      </el-table-column>
      <el-table-column label="案例ID"
                       prop="caseId"
                       width="80">
        <template slot-scope="scope">{{ scope.row.date }}</template>
      </el-table-column>
      <el-table-column prop="sourceCount"
                       label="源SQL总数"
                       width="100">
      </el-table-column>
      <el-table-column prop="targetCount"
                       label="目标SQL总数"
                       show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="sourceExecState"
                       label="源SQL执行状态"
                       width="140">
      </el-table-column>
      <el-table-column prop="targetExecState"
                       label="目标SQL执行状态"
                       width="140">
      </el-table-column>
      <el-table-column prop="sourceSql"
                       label="源SQL"
                       width="140">
      </el-table-column>
      <el-table-column prop="targetCol"
                       label="目标SQL"
                       width="140">
      </el-table-column>
      <el-table-column prop="sourceCol"
                       label="源字段"
                       width="100">
      </el-table-column>
      <el-table-column prop="targetCol"
                       label="目标字段"
                       width="100">
      </el-table-column>
      <el-table-column prop="sourceKey"
                       label="源SQL"
                       width="140">
      </el-table-column>
      <el-table-column prop="targetKey"
                       label="目标SQL"
                       width="140">
      </el-table-column>
      <el-table-column prop="sourceData"
                       label="原数据源"
                       width="140">
      </el-table-column>
      <el-table-column prop="targetData"
                       label="目标数据源"
                       width="140">
      </el-table-column>
      <el-table-column prop="createUser"
                       label="创建者"
                       width="130">
      </el-table-column>

      <el-table-column prop="createTime"
                       label="创建时间"
                       width="130">
      </el-table-column>
      <!-- <el-table-column prop=""
                       label=""
                       width="">
      </el-table-column> -->
    </el-table>

  </section>
</template>

<script>
export default {
  data: function () {
    return {
      tableData3: [{
        caseId: '1',
        sourceCount: '11',
        targetCount: '11',
        sourceExecState: 'running',
        targetExecState: '',
        sourceSql: '',
        targetSql: 'select name from user',
        sourceCol: 'select username from user',
        targetCol: 'username',
        sourceKey: 'name',
        targetKey: '',
        sourceData: '',
        targetData: '',
        createUser: '',
        createTime: ''

      }],
      multipleSelection: []
    }

  },
  methods: {
    toggleSelection (rows) {
      if (rows) {
        rows.forEach(row => {
          this.$refs.multipleTable.toggleRowSelection(row);
        });
      } else {
        this.$refs.multipleTable.clearSelection();
      }
    },
    handleSelectionChange (val) {
      this.multipleSelection = val;
    }
  }

}
</script>

<style>
</style>
