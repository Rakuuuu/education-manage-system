<template>
  <div class="lowin  lowin-blue">
    <div class="lowin-wrapper">
      <div class="lowin-box lowin-register">
        <div class="lowin-box-inner">
          <el-form ref="registerForm" :model="registerForm" :rules="rules">
            <p>教学管理系统</p>
            <div class="lowin-group">
              <label>用户名 </label>
              <el-input ref="userName"  class="lowin-input" placeholder="用户名"
                        v-model="registerForm.userName"
                        name="userName" type="text" tabindex="1" auto-complete="on"/>
            </div>
            <div class="lowin-group password-group">
              <label>密码</label>
              <el-input
                class="lowin-input"
                type="password"
                ref="password"
                placeholder="密码"
                name="password"
                tabindex="2"
                auto-complete="on"
                v-model="registerForm.password"
              />
            </div>
            <div class="lowin-group">
              <label>年级 </label>
              <el-select class="lowin-input"  placeholder="年级" v-model="registerForm.userLevel">
                <el-option v-for="item in levelEnum" :key="item.key" :value="item.key"
                           :label="item.value"></el-option>
              </el-select>
            </div>
            <el-button type="text" class="lowin-btn login-btn" @click="handleRegister">注册</el-button>
            <div class="text-foot">
              已有账号?
              <router-link to="/login" class="login-link">
                登录
              </router-link>
            </div>
          </el-form>
        </div>
      </div>
    </div>
    <div class="account-foot-copyright">
    </div>
  </div>
</template>

<script>
import { mapMutations, mapState } from 'vuex'
import registerApi from '@/api/register'

export default {
  name: 'Login',
  data () {
    const validateUsername = (rule, value, callback) => {
      if (value.length < 5) {
        callback(new Error('用户名不能少于5个字符'))
      } else {
        callback()
      }
    }
    const validatePassword = (rule, value, callback) => {
      if (value.length < 5) {
        callback(new Error('密码不能少于5个字符'))
      } else {
        callback()
      }
    }
    return {
      registerForm: {
        userName: '',
        password: '',
        userLevel: 1
      },
      rules: {
        userName: [{ required: true, trigger: 'blur', validator: validateUsername }],
        password: [{ required: true, trigger: 'blur', validator: validatePassword }]
      }
    }
  },
  methods: {
    handleRegister () {
      let _this = this
      this.$refs.registerForm.validate(valid => {
        if (valid) {
          this.loading = true
          registerApi.register(this.registerForm).then(function (result) {
            console.log(result)
            if (result && result.code === 1) {
              _this.$message.success('注册成功')
              setTimeout(() => {
                _this.$router.push({ path: '/login' })
              }, 2000)
            } else {
              _this.loading = false
              _this.$message.error(result.message)
            }
          }).catch(function (reason) {
            _this.loading = false
          })
        } else {
          return false
        }
      })
    },
    ...mapMutations('user', ['setUserName'])
  },
  computed: {
    ...mapState('enumItem', {
      levelEnum: state => state.user.levelEnum
    })
  }
}
</script>

<style lang="scss">
  .lowin-input {
    .el-input__inner {
      background-color: transparent !important;
      border: 0px !important;
    }
  }
</style>

<style scoped>

  .lowin {
    /* variables */
    --color-primary: #5db96b;
    --color-grey: rgba(0, 198, 99, 0.7);
    --color-dark: rgba(232, 255, 194, 0.7);
    --color-semidark: rgb(182, 182, 182);

    text-align: center;
    margin: 20px 0 0 0;
    font-family: 'Segoe UI';
    font-size: 14px;
  }

  .lowin .lowin-wrapper {
    -webkit-transition: all 1s;
    -o-transition: all 1s;
    transition: all 1s;
    -webkit-perspective: 1000px;
    perspective: 1000px;
    position: relative;
    height: 100%;
    width: 360px;
    margin: 0 auto;
  }

  .lowin.lowin-blue {
    --color-primary: #d3efd5;
    --color-grey: rgba(0, 129, 198, .05);
    --color-dark: rgba(26, 198, 0, 0.7);
    --color-semidark: rgba(65, 159, 121, 0.7);
  }

  .lowin a {
    color: var(--color-primary);
    text-decoration: none;
    border-bottom: 1px dashed var(--color-semidark);
    margin-top: -3px;
    padding-bottom: 2px;
  }

  .lowin * {
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
  }

  .lowin .lowin-brand {
    overflow: hidden;
    width: 100px;
    height: 100px;
    margin: 0 auto -50px auto;
    border-radius: 50%;
    -webkit-box-shadow: 0 4px 40px rgba(0, 0, 0, .07);
    box-shadow: 0 4px 40px rgba(0, 0, 0, .07);
    padding: 10px;
    background-color: #fff;
    z-index: 1;
    position: relative;
  }

  .lowin .lowin-brand img {
    width: 100%;
  }

  .lowin .lowin-box {
    width: 100%;
    position: absolute;
    left: 0;
  }

  .lowin .lowin-box-inner {
    background-color: #fff;
    -webkit-box-shadow: 0 7px 25px rgba(0, 0, 0, .08);
    box-shadow: 0 7px 25px rgba(0, 0, 0, .08);
    padding: 25px;
    text-align: left;
    border-radius: 3px;
  }

  /*.lowin .lowin-box::after {*/
  /*  content: ' ';*/
  /*  -webkit-box-shadow: 0 0 25px rgba(0, 0, 0, .1);*/
  /*  box-shadow: 0 0 25px rgba(0, 0, 0, .1);*/
  /*  -webkit-transform: translate(0, -92.6%) scale(.88);*/
  /*  -ms-transform: translate(0, -92.6%) scale(.88);*/
  /*  transform: translate(0, -92.6%) scale(.88);*/
  /*  border-radius: 3px;*/
  /*  position: absolute;*/
  /*  top: 100%;*/
  /*  left: 0;*/
  /*  width: 100%;*/
  /*  height: 100%;*/
  /*  background-color: #fff;*/
  /*  z-index: -1;*/
  /*}*/

  .lowin .lowin-box.lowin-flip {
    -webkit-transform: rotate3d(0, 1, 0, -180deg);
    transform: rotate3d(0, 1, 0, -180deg);
    display: none;
    opacity: 0;
  }

  .lowin .lowin-box p {
    color: var(--color-semidark);
    font-weight: 700;
    margin-bottom: 20px;
    text-align: center;
    font-size: 24px;
  }

  .lowin .lowin-box .lowin-group {
    margin-bottom: 30px;
  }

  .lowin .lowin-box label {
    margin-bottom: 5px;
    display: inline-block;
    width: 100%;
    color: var(--color-semidark);
    font-weight: 700;
  }

  .lowin .lowin-box label a {
    float: right;
  }

  .lowin .lowin-box .lowin-input {
    background-color: var(--color-grey);
    color: var(--color-dark);
    border: none;
    border-radius: 3px;
    padding: 5px 20px;
    width: 100%;
    outline: 0;
  }

  .lowin .lowin-box .lowin-btn {
    display: inline-block;
    width: 100%;
    border: none;
    color: #fff;
    padding: 15px;
    border-radius: 3px;
    background-color: #5db96b;
    -webkit-box-shadow: 0 2px 7px var(--color-semidark);
    box-shadow: 0 2px 7px var(--color-semidark);
    font-weight: 700;
    outline: 0;
    cursor: pointer;
    -webkit-transition: all .5s;
    -o-transition: all .5s;
    transition: all .5s;
  }

  .lowin .lowin-box .lowin-btn:active {
    -webkit-box-shadow: none;
    box-shadow: none;
  }

  .lowin .lowin-box .lowin-btn:hover {
    opacity: .9;
  }

  .lowin .text-foot {
    text-align: center;
    padding: 10px;
    font-weight: 700;
    margin-top: 20px;
    color: var(--color-semidark);
  }

  /* animation */
  .lowin .lowin-box.lowin-animated {
    -webkit-animation-name: LowinAnimated;
    animation-name: LowinAnimated;
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: forwards;
    animation-fill-mode: forwards;
    -webkit-animation-timing-function: ease-in-out;
    animation-timing-function: ease-in-out;
  }

  .lowin .lowin-box.lowin-animatedback {
    -webkit-animation-name: LowinAnimatedBack;
    animation-name: LowinAnimatedBack;
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: forwards;
    animation-fill-mode: forwards;
    -webkit-animation-timing-function: ease-in-out;
    animation-timing-function: ease-in-out;
  }

  .lowin .lowin-box.lowin-animated-flip {
    -webkit-animation-name: LowinAnimatedFlip;
    animation-name: LowinAnimatedFlip;
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: forwards;
    animation-fill-mode: forwards;
    -webkit-animation-timing-function: ease-in-out;
    animation-timing-function: ease-in-out;
  }

  .lowin .lowin-box.lowin-animated-flipback {
    -webkit-animation-name: LowinAnimatedFlipBack;
    animation-name: LowinAnimatedFlipBack;
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: forwards;
    animation-fill-mode: forwards;
    -webkit-animation-timing-function: ease-in-out;
    animation-timing-function: ease-in-out;
  }

  .lowin .lowin-brand.lowin-animated {
    -webkit-animation-name: LowinBrandAnimated;
    animation-name: LowinBrandAnimated;
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: forwards;
    animation-fill-mode: forwards;
    -webkit-animation-timing-function: ease-in-out;
    animation-timing-function: ease-in-out;
  }

  .lowin .lowin-group.password-group {
    -webkit-transition: all 1s;
    -o-transition: all 1s;
    transition: all 1s;
  }

  .lowin .lowin-group.password-group.lowin-animated {
    -webkit-animation-name: LowinPasswordAnimated;
    animation-name: LowinPasswordAnimated;
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: forwards;
    animation-fill-mode: forwards;
    -webkit-animation-timing-function: ease-in-out;
    animation-timing-function: ease-in-out;
    -webkit-transform-origin: 0 0;
    -ms-transform-origin: 0 0;
    transform-origin: 0 0;
  }

  .lowin .lowin-group.password-group.lowin-animated-back {
    -webkit-animation-name: LowinPasswordAnimatedBack;
    animation-name: LowinPasswordAnimatedBack;
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: forwards;
    animation-fill-mode: forwards;
    -webkit-animation-timing-function: ease-in-out;
    animation-timing-function: ease-in-out;
    -webkit-transform-origin: 0 0;
    -ms-transform-origin: 0 0;
    transform-origin: 0 0;
  }

  @media screen and (max-width: 320px) {
    .lowin .lowin-wrapper {
      width: 100%;
    }

    .lowin .lowin-box {
      padding: 0 10px;
    }
  }
</style>
