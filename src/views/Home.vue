<template>
  <div>
    <el-container>
      <el-header class="homeHeader">
        <div class="title">一个小小的人事管理</div>
        <el-dropdown class="userInfo" @command="commandHandler">
  <span class="el-dropdown-link">
    {{ user.name }}<i><img :src="user.userface" alt="" style="box-shadow: 0 0 25px #cac6c6; margin-top: 4px;"></i>
  </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="userInfo">个人中心</el-dropdown-item>
            <el-dropdown-item command="setting">设置</el-dropdown-item>
            <el-dropdown-item command="logout" divided>注销</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </el-header>
      <el-container>
        <el-aside width="200px">
          <el-menu router unique-opened>
            <el-submenu :index="index+''" v-for="(item,index) in routes" v-if="!item.hidden" :key="index">
              <template slot="title">
                <i style="color: #4ec0d5; margin-right:5px;" :class="item.iconCls"></i>
                <span>{{ item.name }}</span>
              </template>
              <el-menu-item :index="child.path" v-for="(child, indexJ) in item.children" :key="indexJ">
                {{ child.name }}
              </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-main>
          <el-breadcrumb separator-class="el-icon-arrow-right" v-show="this.$router.currentRoute.path!=='/home'">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>{{ this.$router.currentRoute.name }}</el-breadcrumb-item>
          </el-breadcrumb>
          <div class="homeWelcome" v-show="this.$router.currentRoute.path==='/home'">
            欢迎来到小小人事管理系统
          </div>
          <router-view class="homeRouterView"/>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      user: JSON.parse(window.sessionStorage.getItem('user'))
    }
  },
  computed: {
    routes() {
      return this.$store.state.routes
    }
  },
  methods: {
    // menuClick(index) {
    //   this.$router.push(index)
    // },
    commandHandler(cmd) {
      if (cmd === 'logout') {
        this.$confirm('此操作将注销登录, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.getRequest('/logout');
          window.sessionStorage.removeItem('user');
          this.$store.commit('initRoutes', [])
          this.$router.replace('/');
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消操作'
          });
        });
      }
    }
  }
}
</script>

<style>
.homeWelcome {
  text-align: center;
  font-size: 50px;
  font-family: 方正博雅刊宋简体, serif;
  color: #4ec0d5;
  padding-top: 40px;
}

.homeHeader {
  background-color: #4ec0d5;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 15px;
  box-sizing: border-box;
  box-shadow: 0 2px 24px 0 rgba(0, 0, 0, 0.3);
}

.homeHeader .title {
  font-size: 25px;
  font-family: 汉仪全唐诗简, serif;
  color: white;
}

.homeHeader .userInfo {
  cursor: pointer;
}

.el-dropdown-link img {
  width: 50px;
  height: 50px;
  border-radius: 24px;
  margin-left: 8px;
}

.el-dropdown-link {
  font-size: 15px;
  display: flex;
  align-items: center;
  color: white;
}

.homeRouterView {
  margin-top: 10px;
}
</style>