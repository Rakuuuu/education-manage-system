<template>
  <div class="container">
    <div class="container-main">
      <el-card>
        <h3>输入签到码以进行签到</h3>
        <el-input v-model="signInCode"/>
        <el-button type="primary" @click="submitSignIn">签到</el-button>
      </el-card>
    </div>
  </div>
</template>

<script>
import userApi from '@/api/user'
import axios from 'axios'
export default {
  name: 'signIn',
  data () {
    return {
      signInCode: ''
    }
  },
  methods: {
    submitSignIn () {
      // userApi.doRecord({
      //   token: this.signInCode,
      //   studentId: 1
      // }).then(res => {
      //   console.log(res)
      // }, err => {
      //   console.log(err)
      // })
      axios({
        method: 'post',
        url: '/api/student/record/dorecord',
        data: {
          token: this.signInCode,
          studentId: 1
        }
      }).then(res => {
        console.log(res)
      }, err => {
        console.log(err)
      })
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
  }

  >>>.el-card>div{
    height: 250px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-direction: column;
  }

  >>>.el-card h3{
    text-align: center;
  }

  >>>.el-card .el-button{
    width: 100%;
    margin-top: 20px;
    justify-self: start;
  }

</style>
