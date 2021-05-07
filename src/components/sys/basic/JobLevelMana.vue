<template>
  <div>
    <div>
      <el-input v-model="jl.name" style="width: 300px; margin-right: 8px;" prefix-icon="el-icon-plus"
                placeholder="添加职称..."></el-input>
      <el-select v-model="jl.titleLevel" placeholder="职称等级" style="margin-right: 8px;">
        <el-option
            v-for="item in titleLevels"
            :key="item"
            :label="item"
            :value="item">
        </el-option>
      </el-select>
      <el-button icon="el-icon-plus" type="primary" @click="addJobLevel" plain>添加</el-button>
    </div>
    <div>
      <el-table
          :data="jls"
          :row-class-name="tableRowClassName"
          border
          stripe
          style="width: 100%; margin-top: 10px;"
          @selection-change="handleSelectionChange">
        <el-table-column
            type="selection"
            width="54">
        </el-table-column>
        <el-table-column
            prop="id"
            label="编号"
            width="56">
        </el-table-column>
        <el-table-column
            prop="name"
            label="职称姓名"
            width="150">
        </el-table-column>
        <el-table-column
            prop="titleLevel"
            label="职称级别">
        </el-table-column>
        <el-table-column
            prop="createDate"
            label="创建时间">
        </el-table-column>
        <el-table-column
            prop="enabled"
            label="是否启用">
          <template slot-scope="scope">
            <el-tag type="success" v-if="scope.row.enabled">已启用</el-tag>
            <el-tag type="danger" v-else>未启用</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            label="操作">
          <template slot-scope="scope">
            <el-button @click="showEditView(scope.row)">编辑</el-button>
            <el-button type="danger" @click="deleteHandler(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-button type="danger" style="margin-top: 10px;" :disabled="multipleSelection.length===0" @click="deleteMany">批量删除</el-button>
    </div>
    <el-dialog
        title="修改职称"
        :visible.sync="dialogVisible"
        width="30%">
      <div>
        <table>
          <tr>
            <td>
              <el-tag size="medium">职 称 名</el-tag>
            </td>
            <td>
              <el-input v-model="updateJl.name" style="margin-left: 8px; margin-top: 2px;"></el-input>
            </td>
          </tr>
          <tr>
            <td>
              <el-tag size="small">职称级别</el-tag>
            </td>
            <td>
              <el-select v-model="updateJl.titleLevel" placeholder="职称等级" style="margin-left: 8px;">
                <el-option
                    v-for="item in titleLevels"
                    :key="item"
                    :label="item"
                    :value="item">
                </el-option>
              </el-select>
            </td>
          </tr>
          <tr>
            <td>
              <el-tag size="small">是否启用</el-tag>
            </td>
            <td>
              <el-switch
                  v-model="updateJl.enabled"
                  active-color="#13ce66"
                  inactive-color="#ff4949"
                  active-text="启用"
                  inactive-text="禁用" style="margin-left: 8px;">
              </el-switch>
            </td>
          </tr>
        </table>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="doUpdate" >确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import {getRequest} from "../../../utils/api";

export default {
  name: "JobLevelMana",
  data() {
    return {
      dialogVisible: false,
      updateJl: {
        name: '',
        titleLevel: '',
        enabled: false
      },
      jl: {
        name: '',
        titleLevel: ''
      },
      jls: [],
      multipleSelection: [],
      titleLevels: [
        '正高级',
        '副高级',
        '中级',
        '初级',
        '员级',
      ]
    }
  },
  mounted() {
    this.initJls()
  },
  methods: {
    deleteMany() {
      this.$confirm('此操作将永久删除'+this.multipleSelection.length+'条记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let ids='?'
        this.multipleSelection.forEach(item=>{
          ids+='ids='+item.id+'&'
        })
        this.deleteRequest('/system/basic/joblevel/'+ids).then(resp=>{
          if(resp) {
            this.initJls()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除！'
        });
      })
    },
    handleSelectionChange(val) {
      this.multipleSelection = val
    },
    doUpdate() {
      this.putRequest('/system/basic/joblevel/',this.updateJl).then(resp=>{
        if(resp) {
          this.initJls()
          this.dialogVisible = false
        }
      })
    },
    tableRowClassName({row}) {
      if (row.enabled === false) {
        return 'warning-row';
      }
      return '';
    },
    showEditView(data) {
      Object.assign(this.updateJl, data)
      this.dialogVisible = true
    },
    deleteHandler(data) {
      this.$confirm('此操作将永久删除 ' + data.name + ' 职称，是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/basic/joblevel/' + data.id).then(resp => {
          if (resp) {
            this.initJls();
          }
        })
        this.$message({
          message: '删除成功！',
          type: 'success'
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    addJobLevel() {
      if (this.jl.name && this.jl.titleLevel) {
        this.postRequest('/system/basic/joblevel/', this.jl).then(resp => {
          if (resp) {
            this.initJls()
          }
        })
      } else {
        this.$message.error("值不能为空哦~~~🤪")
      }

    },
    initJls() {
      this.getRequest('/system/basic/joblevel/').then(resp => {
        if (resp) {
          this.jls = resp
          this.jl = {
            name: '',
            titleLevel: ''
          }
        }
      })
    }
  }
}
</script>

<style>
.el-table .warning-row {
  background: #f2cac9;
}
</style>