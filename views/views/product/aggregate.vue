<template>
  <section>
    <div class="table">
      <ul v-for="(list, i) in table">
        <p>{{ list.name }}</p>
        <ul v-for="(item,j) in list.child">
          <p>{{ item.name }}</p>
          <li
            v-for="(item2, index) in item.child"
            @click="assignment(i,j,index,(i + '-' + j + '-' + index))"
            :class="{active : active == (i + '-' + j + '-'+ index)}"
          >{{ item2.name }}</li>
        </ul>
      </ul>
    </div>
    <div class="t-content">
      <h4>{{ name }}</h4>
      <p>{{ sqlNew }}</p>
      <p>{{ sqlOld }}</p>
      <el-button type="primary" @click="creatTask">新建案例</el-button>
    </div>
    <!-- 创建案例 -->
    <div id="creaqtTask" :class="{creaqtTask:isCreaqtTask}">
      <!-- 产品 -->
      <el-row :gutter="20">
        <el-col :span="24">
          <el-row :gutter="20" style="margin-bottom: 20px;margin-top: 20px;">
            <el-col :span="24">
              <el-button type="primary">执行</el-button>
              <el-button type="primary">添加到任务集</el-button>
              <el-button type="primary" @click="closeCreat()" plain>取消创建</el-button>
            </el-col>
          </el-row>
          <template>
            <!-- <el-form ref="form" :model="form" label-width="80px"> -->
            <el-row :gutter="24">
              <el-col :span="12">
                <el-input type="textarea" :rows="5" placeholder="请输入原SQL语句" v-model="textarea"></el-input>
              </el-col>
              <el-col :span="12">
                <el-input type="textarea" :rows="5" placeholder="请输入目标SQL语句" v-model="textarea2"></el-input>
              </el-col>
              <el-col :span="24">
                <h4>规则</h4>
                <el-button type="primary" @click="ruleShow()">增加规则</el-button>
                <el-table :data="tableData2" style="width: 100%;margin-top:20px;">
                  <el-table-column label="fild" prop="fild"></el-table-column>
                  <el-table-column label="rule" prop="rule"></el-table-column>
                  <el-table-column label="dataOld" prop="dataOld"></el-table-column>
                  <el-table-column label="dataNew" prop="dataNew"></el-table-column>
                  <el-table-column align="right">
                    <template slot="header" slot-scope="scope"></template>
                    <template slot-scope="scope">
                      <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                      <el-button
                        size="mini"
                        type="danger"
                        @click="handleDelete(scope.$index, scope.row)"
                      >删除</el-button>
                    </template>
                  </el-table-column>
                </el-table>
              </el-col>
            </el-row>
            <!-- </el-form> -->
          </template>
        </el-col>
      </el-row>
      <!-- 规则弹窗 -->
      <el-form ref="form" :model="form" id="rule" :class="{ rule:rule }" label-width="80px">
        <el-form-item label="fild">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="规则">
          <el-select style="width:150px;" v-model="form.region" placeholder="请选择规则">
            <el-option label="去除空格" value="规则1"></el-option>
            <el-option label="补齐" value="规则2"></el-option>
          </el-select>
          <el-input style="width:150px;" v-model="form.regionDetail"></el-input>
        </el-form-item>
        <el-form-item label="原字段">
          <el-input v-model="form.field"></el-input>
        </el-form-item>
        <el-form-item label="目标字段">
          <el-input v-model="form.field2"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary">立即创建</el-button>
          <el-button type="primary" @click="ruleHide">取消创建</el-button>
        </el-form-item>
      </el-form>
    </div>
    <!-- 创建案例结束 -->
  </section>
</template>
<script>
export default {
  data() {
    return {
      isCreaqtTask: false,
      table: [
        {
          name: "案例",
          child: [
            {
              name: "案例1",
              child: [
                {
                  name: "事件1",
                  sqlNew: "sql1",
                  sqlOld: "sql2"
                },
                {
                  name: "事件1",
                  sqlNew: "sql1",
                  sqlOld: "sql2-2"
                }
              ]
            },
            {
              name: "案例1",
              child: [
                {
                  name: "事件1",
                  sqlNew: "sql1-3",
                  sqlOld: "sql2-2"
                },
                {
                  name: "事件1",
                  sqlNew: "sql1-4",
                  sqlOld: "sql2-5"
                }
              ]
            }
          ]
        },
        {
          name: "案例",
          child: [
            {
              name: "案例1",
              child: [
                {
                  name: "事件1",
                  sqlNew: "sql1-6",
                  sqlOld: "sql2-7"
                },
                {
                  name: "事件1",
                  sqlNew: "sql1-12",
                  sqlOld: "sql2-12"
                }
              ]
            },
            {
              name: "案例1",
              child: [
                {
                  name: "事件1",
                  sqlNew: "sql1",
                  sqlOld: "sql2"
                },
                {
                  name: "事件1",
                  sqlNew: "sql1",
                  sqlOld: "sql2"
                }
              ]
            }
          ]
        },
        {
          name: "案例",
          child: [
            {
              name: "案例1",
              child: [
                {
                  name: "事件1",
                  sqlNew: "sql1",
                  sqlOld: "sql2"
                },
                {
                  name: "事件1",
                  sqlNew: "sql1",
                  sqlOld: "sql2"
                }
              ]
            },
            {
              name: "案例1",
              child: [
                {
                  name: "事件1",
                  sqlNew: "sql1",
                  sqlOld: "sql2"
                },
                {
                  name: "事件1",
                  sqlNew: "sql1",
                  sqlOld: "sql2"
                }
              ]
            }
          ]
        }
      ],
      ins: "",
      name: "",
      sqlNew: "",
      sqlOld: "",
      active: "",
      options: [
        {
          value: "zhinan",
          label: "指南",
          children: [
            {
              value: "shejiyuanze",
              label: "设计原则"
            },
            {
              value: "daohang",
              label: "导航"
            }
          ]
        },
        {
          value: "ziyuan2",
          label: "资源2",
          children: [
            {
              value: "axure",
              label: "Axure Components"
            },
            {
              value: "sketch",
              label: "Sketch Templates"
            },
            {
              value: "jiaohu",
              label: "组件交互文档"
            }
          ]
        },
        {
          value: "ziyuan",
          label: "资源",
          children: [
            {
              value: "axure",
              label: "Axure Components"
            },
            {
              value: "sketch",
              label: "Sketch Templates"
            },
            {
              value: "jiaohu",
              label: "组件交互文档"
            }
          ]
        }
      ],
      form: {
        name: "",
        region: "",
        regionDetail: "",
        field: "",
        field2: ""
      },
      textarea: "",
      textarea2: "",
      rule: false,
      tableData: [
        {
          fild: 1,
          name: "案例1"
        },
        {
          fild: 2,
          name: "案例1"
        },
        {
          fild: 3,
          name: "案例1"
        },
        {
          fild: 4,
          name: "案例1"
        }
      ],
      tableData2: [
        {
          fild: "1",
          rule: "规则",
          dataOld: "案例1",
          dataNew: "案例2"
        }
      ]
    };
  },
  methods: {
    assignment(i, j, index, k) {
      this.name = this.table[i].child[j].child[index].name;
      this.sqlNew = this.table[i].child[j].child[index].sqlNew;
      this.sqlOld = this.table[i].child[j].child[index].sqlOld;
      this.active = k;
    },
    creatTask() {
      this.isCreaqtTask = true;
    },
    closeCreat() {
      this.isCreaqtTask = false;
    },
    ruleShow() {
      this.rule = true;
    },
    ruleHide() {
      this.rule = false;
    },
    handleChange(value) {
      console.log(value);
    },
    handleEdit(index, row) {
      console.log(index, row);
    },
    handleDelete(index, row) {
      console.log(index, row);
    }
  }
};
</script>
<style lang="scss" type="text/css">
@import "~scss_vars";
::-webkit-scrollbar {
  width: 0px;
  height: 1px;
}
::-webkit-scrollbar-thumb {
  border-radius: 5px;
  -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
  background: rgba(0, 0, 0, 0.2);
}
.table {
  background: $color-bac;
  width: 200px;
  height: calc(100% - 80px);
  overflow-y: scroll;
  position: absolute;
  /*margin-top: 20px;*/
  padding: 0;
  ul,
  li,
  p {
    padding: 0;
    margin: 0;
    list-style: none;
  }
  p {
    background: $color-bac-hover;
    padding: 10px;
  }
  ul {
    color: #333;
    ul {
      p {
        background: #f4f5f6;
        padding-left: 30px;
      }
    }
    li {
      cursor: pointer;
      color: #48576a;
      padding: 10px 10px 10px 50px;
      &:hover {
        color: $color-primary;
      }
      &.active {
        color: $color-primary;
      }
    }
  }
}
.t-content {
  margin-top: 20px;
  margin-left: 220px;
}

#creaqtTask {
  display: none;
  background: $color-major;
  z-index: 13;
  position: absolute;
  top: 20px;
  height: 100%;
  left: 250px;
  &.creaqtTask {
    display: inline-block;
  }
}
#rule {
  position: absolute;
  top: 0;
  height: 300px;
  width: 400px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.4);
  padding: 20px;
  border-radius: 10px;
  z-index: 11;
  left: 20%;
  background: $color-major;
  display: none;
  &.rule {
    display: inline-block;
  }
}
#task {
  /*// position: absolute;
  // top: 0;
  // height: 100%;
  // width: 100%;*/
  z-index: 10;
  left: 0;
  background: $color-major;
  /*// display: none;
  // &.taskActive {
  //  display: inline-block;
  // }*/
}
.text-right {
  text-align: right;
}
</style>