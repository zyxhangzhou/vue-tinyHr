<template>
  <div>
    <div>
      <el-input placeholder="请输入角色英文名" v-model="role.name" style="width: 30%; margin-right: 8px;">
        <template slot="prepend">ROLE_</template>
      </el-input>
      <el-input placeholder="请输入角色中文名" v-model="role.nameZh" style="width: 30%; margin-right: 8px;" @keydown.enter.native="doAddRole"></el-input>
      <el-button type="primary" icon="el-icon-plus" @click="doAddRole" plain>添加角色</el-button>
    </div>
    <div style="margin-top: 10px; width: 70%">

      <el-collapse v-model="activeName" accordion @change="change">
        <el-collapse-item :title="r.nameZh" :name="r.id" v-for="(r, index) in roles" :key="index">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>可访问的资源</span>
              <el-button style="float: right; padding: 3px 0; color: #f19790;"
                         icon="el-icon-delete" type="text" @click="deleteRole(r)"></el-button>
            </div>
            <div>
              <el-tree show-checkbox
                       node-key="id"
                       ref="tree"
                       :key="index"
                       :default-checked-keys="selectedMenus"
                       :data="allMenus" :props="defaultProps"></el-tree>
              <div style="display: flex;justify-content:flex-end;">
                <el-button type="primary" @click="cancelUpdate" plain>取消修改</el-button>
                <el-button type="danger" @click="doUpdate(r.id, index)" plain>确认修改</el-button>
              </div>
            </div>
          </el-card>
        </el-collapse-item>
      </el-collapse>
    </div>
  </div>
</template>

<script>
export default {
  name: "PermissionMana",
  data() {
    return {
      role: {
        name: '',
        nameZh: ''
      },
      activeName: -1,
      roles: [],
      allMenus: [],
      selectedMenus: [],
      defaultProps: {
        children: 'children',
        label: 'name'
      },
    }
  },
  mounted() {
    this.initRoles()
  },
  methods: {
    deleteRole(role) {
      this.$confirm('此操作将删除【'+role.nameZh+'】角色, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/basic/permission/role/'+role.id).then(resp=>{
          if(resp) {
            this.initRoles()
            // this.activeName = -1
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消修改'
        });
      })
    },
    doAddRole() {
      if(this.role.name && this.role.nameZh) {
        this.postRequest('/system/basic/permission/role', this.role).then(resp=>{
          if(resp) {
            this.role.name=''
            this.role.nameZh=''
            this.initRoles()
          }
        })
      } else {
        this.$message.error('不可添加空值！')
      }
    },
    cancelUpdate() {
      this.activeName = -1;
    },
    doUpdate(rid, index) {
      let tree = this.$refs.tree[index]
      let selectedKeys = tree.getCheckedKeys(true)
      let url ='/system/basic/permission/?rid='+rid
      selectedKeys.forEach(key=>{
        url+='&mids='+key
      })
      this.$confirm('此操作将更改若干权限, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.putRequest(url).then(resp=>{
          if(resp) {
            // this.initRoles()
            this.activeName = -1
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消修改'
        });
      })
    },
    change(rid) {
      if (rid) {
        this.initAllMenus()
        this.initSelectedMenus(rid)
      }
    },
    initSelectedMenus(rid) {
      this.getRequest('/system/basic/permission/mids/' + rid).then((resp) => {
        if (resp) {
          this.selectedMenus = resp
        }
      })
    },
    initAllMenus() {
      this.getRequest('/system/basic/permission/menus').then((resp) => {
        if (resp) {
          this.allMenus = resp;
        }
      })
    },
    initRoles() {
      this.getRequest('/system/basic/permission/').then((resp) => {
        this.roles = resp;
      })
    }
  }
}
</script>

<style>
</style>