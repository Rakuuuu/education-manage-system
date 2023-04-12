<template>
  <el-container>
    <header class="student-header">
      <div class="header-main">
        <div class="header-left">
          <div class="logo">
          <a><img src="@/assets/logo.png" height="32"/></a>
        </div>
          <el-menu class="el-menu-title" mode="horizontal" :default-active="defaultUrl" :router="true">
            <el-menu-item index="/index">首页</el-menu-item>
            <el-menu-item index="/paper/index">试题</el-menu-item>
            <el-menu-item index="/record/index">考试记录</el-menu-item>
            <el-menu-item index="/question/index">错题本</el-menu-item>
          </el-menu>
        </div>
        <div class="head-user">
          <el-dropdown trigger="click" placement="bottom">
            <el-badge :is-dot="messageCount!==0" >
              <img
                class="el-dropdown-avatar"
                :src="userInfo.imagePath === null ? require('@/assets/avatar.png') : userInfo.imagePath"
              />
            </el-badge>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item @click.native="$router.push({path:'/user/index'})">个人中心</el-dropdown-item>
              <el-dropdown-item @click.native="$router.push({path:'/user/message'})">
                <el-badge :value="messageCount" v-if="messageCount!==0">
                  <span>消息中心</span>
                </el-badge>
                <span  v-if="messageCount===0">消息中心</span>
              </el-dropdown-item>
              <el-dropdown-item @click.native="logout" divided>退出</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
      </div>
    </header>
    <el-main class="student-main">
      <router-view/>
    </el-main>
    <el-footer height="340" class="student-footer">
      <div class="foot-container">
        <div class="footer-main">
        </div>
        <div class="footer-main">
        </div>
        <div class="footer-main">
        </div>
        <div class="footer-main">
        </div>
      </div>
    </el-footer>
    <div class="foot-copyright">
    </div>
  </el-container>
</template>

<script>
import { mapActions, mapMutations, mapState } from 'vuex'
import loginApi from '@/api/login'
import userApi from '@/api/user'
export default {
  name: 'Layout',
  data () {
    return {
      defaultUrl: '/index',
      userInfo: {
        imagePath: null
      }
    }
  },
  created () {
    let _this = this
    this.defaultUrl = this.routeSelect(this.$route.path)
    this.getUserMessageInfo()
    userApi.getCurrentUser().then(re => {
      _this.userInfo = re.response
    })
  },
  watch: {
    $route (to, from) {
      this.defaultUrl = this.routeSelect(to.path)
    }
  },
  methods: {
    routeSelect (path) {
      let topPath = ['/', '/index', '/paper/index', '/record/index', '/question/index']
      if (topPath.indexOf(path)) {
        return path
      }
      return null
    },
    logout () {
      let _this = this
      loginApi.logout().then(function (result) {
        if (result && result.code === 1) {
          _this.clearLogin()
          _this.$router.push({ path: '/login' })
        }
      })
    },
    ...mapActions('user', ['getUserMessageInfo']),
    ...mapMutations('user', ['clearLogin'])
  },
  computed: {
    ...mapState('user', {
      messageCount: state => state.messageCount
    })
  }
}
</script>

<style lang="scss" scoped>
.student-header{
  height:80px;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  min-width: 1200px;
}
.header-main{
  width: 1200px;
  height: fit-content;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header-left{
  display: flex;
  align-items: center;
  flex-shrink: 0;
}

.logo{
  flex-shrink: 0;
  align-self: center;
  justify-content: center;
  padding: 0 20px 0 0 !important;
}

.logo a{
  height: 100%;
  width: 100%;
  display: flex;
}

.logo a img{
  justify-content: center;
  align-items: center;
}

.el-dropdown-avatar{
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 1px #e0e0e0 solid;
}
  .el-menu-item{
    border-bottom: 0px solid #409EFF !important;
    position: relative;
  }

  .el-menu-item.is-active{
    border-bottom: 0px solid #409EFF !important;
    color: #5cb76a !important;
  }

  .el-menu-item.is-active:after{
    position: absolute;
    bottom: 15%;
    left: 50%;
    width: 20px;
    height:5px;
    background: #5cb76a;
    border-radius: 2.5px;
    content: '';
    transform: translate(-50%, 0);
    animation: appear 0.75s linear;
  }

  @keyframes appear {
    0%{
      background: #ffffff;
    }
    25%{
      background: #d3f1d6;
    }
    50%{
      background: #a4e8af;
    }
    75%{
      background: #8fc096;
    }
    100%{
      background: #5cb76a;
    }
  }

.student-header.head-user.el-dropdown-avatar{
    margin: 0 !important;
    flex-shrink: 0 !important;
  }
</style>
