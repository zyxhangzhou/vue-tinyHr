<template>
  <div>
    <div style="margin-top: 10px; margin-bottom: 38px; display: flex; justify-content: center;">
      <el-input v-model="keywords" placeholder="通过用户名搜索用户..." prefix-icon="el-icon-search"
                style="margin-right: 10px; width: 40%" @keydown.enter.native="doSearch"></el-input>
      <el-button icon="el-icon-search" type="primary" @click="doSearch" plain>搜索</el-button>
    </div>
    <div class="hrContainer">
      <el-card class="hrCard" v-for="(hr, index) in hrs" :key="index">
        <div slot="header" class="clearfix">
          <span>{{ hr.name }}</span>
          <el-button style="float: right; padding: 3px 0; color: #ff6d78" type="text"
                     icon="el-icon-delete" @click="deleteHr(hr)">删除操作员
          </el-button>
        </div>
        <div>
          <div style="display: flex; justify-content:center;">
            <el-avatar shape="circle" :size="100" :src="hr.userface" :alt="hr.name"
                       style="box-shadow: 0 0 25px #cac6c6;"></el-avatar>
          </div>
          <div class="userInfoContainer">
            <div>用户名：{{ hr.name }}</div>
            <div>手机号码：{{ hr.phone }}</div>
            <div>电话号码：{{ hr.telephone }}</div>
            <div>地址：{{ hr.address }}</div>
            <div>用户状态：
              <el-switch
                  style="display: block"
                  v-model="hr.enabled"
                  @change="handleChange(hr)"
                  active-color="#13ce66"
                  inactive-color="#ff4949"
                  active-text="启用"
                  inactive-text="禁用">
              </el-switch>
            </div>
            <div>用户角色：
              <el-tag type="success" size="small" style="margin-right: 5px;" v-for="(role, indexJ) in hr.roles"
                      :key="indexJ">{{ role.nameZh }}
              </el-tag>
              <el-popover
                  placement="right"
                  title="角色列表"
                  @show="showPop(hr)"
                  @hide="hidePop(hr)"
                  width="200"
                  trigger="click">
                <el-select v-model="selectedRoles" multiple placeholder="请选择">
                  <el-option
                      v-for="(r, indexK) in allRoles"
                      :key="indexK"
                      :label="r.nameZh"
                      :value="r.id">
                  </el-option>
                </el-select>
                <el-button slot="reference" icon="el-icon-more" type="text"></el-button>
              </el-popover>

            </div>
            <div>备注：{{ hr.remark }}</div>
          </div>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
export default {
  name: "SysHr",
  data() {
    return {
      keywords: '',
      hrs: [],
      allRoles: [],
      selectedRoles: []
    }
  },
  mounted() {
    this.initHrs()
  },
  methods: {
    deleteHr(hr) {
      this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/hr/' + hr.id).then(resp => {
          if (resp) {
            this.initHrs()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    doSearch() {
      this.initHrs()
    },
    hidePop(hr) {
      let roles = [];
      Object.assign(roles, hr.roles);
      let flag = false;
      if (roles.length !== this.selectedRoles.length) {
        flag = true;
      } else {
        for (let i = 0; i < roles.length; i++) {
          let role = roles[i];
          for (let j = 0; j < this.selectedRoles.length; j++) {
            let sr = this.selectedRoles[j];
            if (role.id === sr) {
              roles.splice(i, 1);
              i--;
              break;
            }
          }
        }
        if (roles.length !== 0) {
          flag = true;
        }
      }
      if (flag) {
        let url = "/system/hr/role?hrid=" + hr.id;
        this.selectedRoles.forEach((sr) => {
          url += "&rids=" + sr;
        });
        this.putRequest(url).then((resp) => {
          if (resp) {
            this.initHrs();
          }
        });
      }
    },
    showPop(hr) {
      this.initAllRoles()
      let roles = hr.roles
      this.selectedRoles = []
      roles.forEach(r => {
        this.selectedRoles.push(r.id)
      })
    },
    handleChange(hr) {
      delete hr.roles
      this.putRequest('/system/hr/', hr).then(resp => {
        if (resp) {
          this.initHrs()
        }
      })
    },
    initAllRoles() {
      this.getRequest('/system/hr/roles').then(resp => {
        if (resp) {
          this.allRoles = resp
        }
      })
    },
    initHrs() {
      this.getRequest('/system/hr/?keywords=' + this.keywords).then(resp => {
        if (resp) {
          this.hrs = resp
        }
      })
    }
  }
}
</script>

<style>
.userInfoContainer div {
  margin-bottom: 4px;
  text-align: center;
  font-size: 16px;
  color: #609599;
}

.userInfoContainer {
  margin-top: 20px;
}

.hrContainer {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
}

.hrCard {
  width: 30%;
  margin-bottom: 20px;
}
</style>