<template>
  <div class="template-manage">
    <div class="temp-title">
      <p class="icon-box">
        <img class="icon-img" src="@/assets/images/set.svg" />
        <span class="name">模板管理</span>
      </p>
      <el-button 
        size="small" 
        type="primary"
        icon="el-icon-circle-plus-outline"
        @click="addTemp">新增模板</el-button>
    </div>
    <div class="temp-list">
      <el-table
        class="template-list"
        :data="tempList"
        style="width: 100%">
        <el-table-column
          label="名称">
          <template slot-scope="scope">
            <div v-if="!scope.row.editing">
              <span>{{ scope.row.name }}</span>
            </div>
            <div v-else>
              <el-input v-model="scope.row.name" placeholder="请填写模板名称"></el-input>
            </div>
          </template>
        </el-table-column>
        <el-table-column
          label="影像类型">
          <template slot-scope="scope">
            <div v-if="!scope.row.editing">
              <span>{{ scope.row.pre }}</span>
            </div>
            <div v-else>
              <el-select v-model="scope.row.pre" placeholder="影像类型">
                <el-option
                  v-for="item in imageType"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </div>
          </template>
        </el-table-column>
        <el-table-column
          label="增强处理">
          <template slot-scope="scope">
            <div v-if="!scope.row.editing">
              <span>{{ scope.row.next }}</span>
            </div>
            <div v-else>
              <el-select v-model="scope.row.next" placeholder="增强处理">
                <el-option
                  v-for="item in enhanceDeal"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </div>
          </template>
        </el-table-column>
        <el-table-column
          label="操作">
          <template slot-scope="scope">
            <div class="operate-groups">
              <el-button
                type="primary"
                size="mini"
                v-if="!scope.row.editing"
                icon="el-icon-edit-outline"
                @click="handleEdit(scope.$index, scope.row)">编辑
              </el-button>
              <el-button
                type="primary"
                size="mini"
                v-if="scope.row.editing"
                icon="el-icon-success"
                @click="handleSave(scope.$index, scope.row)">保存
              </el-button>
              <el-button
                size="mini"
                type="danger"
                v-if="!scope.row.editing"
                icon="el-icon-delete"
                @click="handleDelete(scope.$index, scope.row)">删除
              </el-button>
              <el-button
                size="mini"
                type="warning"
                v-if="scope.row.editing"
                icon="el-icon-warning"
                @click="handleCancel(scope.$index, scope.row)">取消
              </el-button>
              <div class="upAndDown">
                <el-button 
                  plain 
                  class="up" 
                  type="primary" 
                  size="mini"
                  icon="el-icon-arrow-up"
                  @click="handleUp(scope.$index, scope.row)">
                </el-button>
                <el-button 
                  plain   
                  class="down" 
                  type="primary" 
                  size="mini" 
                  icon="el-icon-arrow-down"
                  @click="handleDown(scope.$index, scope.row)">
                </el-button>
              </div>
            </div>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'
export default {
  data () {
    return {
      tempList: [],
      imageType: [{
        label: 'CT 头颅平扫',
        value: 'CT 头颅平扫'
      }, {
        label: 'CT 腹部',
        value: 'CT 腹部'
      }, {
        label: 'CT 胸部',
        value: 'CT 胸部'
      }, {
        label: 'CT 脊柱',
        value: 'CT 脊柱'
      }, {
        label: '胸腺钼靶',
        value: '胸腺钼靶'
      }, {
        label: 'MRI',
        value: 'MRI'
      }],
      enhanceDeal: [{
        label: 'SAGAN V1',
        value: 'SAGAN V1'
      }, {
        label: 'SAGAN V2',
        value: 'SAGAN V2'
      }, {
        label: 'SAGAN V3',
        value: 'SAGAN V3'
      }, {
        label: 'SAGAN V4',
        value: 'SAGAN V4'
      }]
    }
  },
  created () {
    this.tempList = JSON.parse(localStorage.getItem('tempList'))
  },
  methods: {
    // 上下自由调整表格数据
    swapItems (arr, index1, index2) {
      arr[index1] = arr.splice(index2, 1, arr[index1])[0]
      return arr
    },
    handleUp ($index, row) {
      if ($index === 0) {
        return
      }
      this.swapItems(this.tempList, $index, $index - 1)
    },
    handleDown ($index, row) {
      if ($index === this.tempList.length - 1) {
        return
      }
      this.swapItems(this.tempList, $index, $index + 1)
    },
    // 编辑
    handleEdit ($index, row) {
      this.$set(this.tempList[$index], 'editing', true)
    },
    // 保存
    handleSave ($index, row) {
      this.$set(this.tempList[$index], 'editing', false)
      localStorage.setItem('tempList', JSON.stringify(this.tempList))
    },
    // 取消
    handleCancel ($index, row) {
      this.$set(this.tempList[$index], 'editing', false)
    },
    // 新增一条模板数据
    addTemp () {
      debugger
      this.tempList = this.tempList || []
      this.tempList.push({
        name: '',
        pre: '',
        next: '',
        editing: true
      })
    },
    // 删除
    handleDelete ($index, row) {
      this.$confirm('此操作将永久删除该条模板, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.tempList.splice($index, 1)
        localStorage.setItem('tempList', JSON.stringify(this.tempList))
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch((err) => {
        this.$message({
          type: 'error',
          message: err
        })
      })
    }
  }
}
</script>

<style scoped lang="scss">
.template-manage {
  width: 80%;
  margin: 0 auto;
  .temp-title {
    display: flex;
    justify-content: space-between;
    height: 30px;
    margin-top: 20px;
  }
  .el-table {
    background-color: #f7f7f7;
    margin-top: 20px;
    th>.cell {
      text-align: center !important; 
   }
  }
  .operate-groups {
    display: flex;
    justify-content: flex-start;
    .upAndDown {
      display: flex;
      flex-direction: column;
      margin-left: 10px;
      .el-button--mini {
        padding: 0;
        width: 30px;
        margin-left: 0;
      }
      .down {
        margin-top: 2px;
      }
    }
  }
}
</style>
