<template>
  <div class="container">
    <div class="container-main">
      <el-card>
        <h3>在线签到</h3>
        <div>
          <el-input v-model="signInCode" placeholder="请输入签到码"/>
          <el-button @click="submitSignIn">签到</el-button>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
import userApi from '@/api/user'
// import axios from 'axios'
export default {
  name: 'signIn',
  data () {
    return {
      signInCode: '',
      userData: {}
    }
  },
  mounted () {
    userApi.getCurrentUser().then(res => {
      console.log(res)
      if (res.response) {
        this.userData = res.response
      } else {
        this.$message.warning('发生错误')
      }
    })
  },
  methods: {
    submitSignIn () {
      userApi.doRecord({
        token: parseInt(this.signInCode),
        studentId: this.userData.id
      }).then(res => {
        if (res.code === 1) { this.$message.success(res.message) } else { this.$message.warning(res.message) }
      }, err => {
        console.log(err)
      })
      // axios({
      //   method: 'post',
      //   url: '/api/student/record/dorecord',
      //   data: {
      //     token: this.signInCode,
      //     studentId: 1
      //   }
      // }).then(res => {
      //   console.log(res)
      // }, err => {
      //   console.log(err)
      // })
    }
  }
}
</script>

<style scoped>
  .container{
    width: 100%;
    max-width: 2440px;
  }

  .container-main{
    width: 1200px;
    margin: 0 auto;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 30px 0;
  }

  .el-card{
    width: 400px !important;
    height: 400px !important;
    display: flex;
    justify-content: center;
    align-items: center;
    background: url(~@/assets/bg.gif) top center no-repeat;
    background-size: 100% 100%;
    overflow: hidden;
    border: 0 solid;
    box-shadow: 0 10px 20px rgba(0,0,0,.3);
  }

  >>>.el-card>div{
    height: 300px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-direction: column;
  }

  >>>.el-card h3{
    text-align: center;
    letter-spacing: 0.75rem;
    margin:30px -0.5rem 0 0 ;
    font-size: 48px;
    color: #444444;
  }

  >>>.el-card .el-button{
    width: 100%;
    margin-top: 40px;
    justify-self: start;
  }

</style>
