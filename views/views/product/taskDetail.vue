<template>
  <section>
    <p class="fanhui">
      <span @click="product()">产品</span> /
      <span @click="product()">子产品</span> /
      <span @click="product()">测试集</span> /
      <span style="color: #97a8be">案例列表</span>
    </p>
    <el-button type="primary" plain @click="returnP()">返回</el-button>
  </section>
</template>
<script type="text/javascript">
export default {
  data() {
      return {

      }
  },
  methods: {

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
<style media="screen">

</style>
