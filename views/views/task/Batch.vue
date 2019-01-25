
<template>
  <section>

    <el-table ref="multipleTable"
              :data="tableData"
              tooltip-effect="dark"
              style="width: 100%"
              @selection-change="handleSelectionChange">
      <el-table-column type="selection"
                       width="55">
      </el-table-column>
      <el-table-column label="任务ID"
                       width="120">
        <template slot-scope="scope">{{ scope.row.taskID }}</template>
      </el-table-column>
      <el-table-column prop="taskName"
                       label="任务名称"
                       width="120">
      </el-table-column>
      <el-table-column prop="numbers"
                       label="案例数量">
      </el-table-column>

    </el-table>
  </section>
</template>

<script>
export default {
  data: function () {
    return {
      tableData: [{

        taskName: 'task1',
        numbers: '1111',

      },
      {

        taskName: 'task1',
        numbers: '1111',
      }],
      multipleSelection: []

    }

  },
  methods: {    multipleSelection: []
  }
}
</script>

<style>
</style>


