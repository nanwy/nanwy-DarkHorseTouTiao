<template>
  <div class="login-container">
    <!-- 顶部导航 -->
    <van-nav-bar
      class="app-nav-bar"
      title="登录"
      left-arrow
      @click-left="$router.back()"
    />

    <!-- 登录表单 -->
    <van-form validate-first ref="login-form">
      <van-cell-group>
        <van-field
          v-model="user.mobile"
          type="tel"
          maxlength="11"
          icon-prefix="toutiao"
          left-icon="shouji"
          placeholder="请输入手机号"
          ref="mobile"
          name="mobile"
        />
        <van-field
          v-model="user.code"
          type="tel"
          maxlength="6"
          clearable
          icon-prefix="toutiao"
          left-icon="yanzhengma"
          placeholder="请输入验证码"
          ref="code"
          name="code"
        >
          <template #button>
            <van-count-down
              v-if="isCountDownShow"
              :time="1000 * 60"
              format="ss s"
              @finish="isCountDownShow = false"
            />
            <van-button
              v-else
              class="send-btn"
              size="mini"
              round
              :loading="isSendSmsLoading"
              @click.prevent="onSendSms"
              >发送验证码</van-button
            >
          </template>
        </van-field>
      </van-cell-group>
    </van-form>
    <div class="login-wrapper">
      <van-button class="login-btn" type="info" block @click="onLogin"
        >登录</van-button
      >
    </div>
  </div>
</template>

<script>
import { login, sendSms } from '@/api/user'
export default {
  name: 'LoginIndex',
  data() {
    return {
      user: {
        mobile: '',
        code: ''
      },
      isSendSmsLoading: false, // 防止网络慢的用户暴力操作，给发送验证码按钮添加一个loading效果
      isCountDownShow: false // 倒计时显示状态
    }
  },
  methods: {
    async onLogin() {
      if (!this.checkMobile() || !this.checkCode()) {
        return
      }
      this.$toast.loading({
        duration: 0, // 持续时间，0表示持续展示不停止
        forbidClick: true, // 是否禁止背景点击
        message: '登录中...' // 提示消息
      })

      try {
        const { data } = await login(this.user)
        this.$toast.success('登录成功')
        this.$store.commit('setUser', data.data)
        this.$router.push('/')
      } catch (err) {
        if (err.response.status === 400) {
          this.$toast.fail('登录失败，手机号或验证码错误')
        }
      }
    },
    checkMobile() {
      const { mobile } = this.user
      if (!mobile) {
        this.$toast({
          message: '手机号不能为空',
          position: 'top'
        })
        this.$refs['mobile'].focus()
        return false
      }
      if (!/^1[3578]\d{9}$/.test(mobile)) {
        this.$toast({
          message: '手机号码格式错误'
        })
        this.$refs.mobile.focus()
        return false
      }
      return true
    },
    checkCode() {
      const { code } = this.user
      if (!code) {
        this.$toast({
          message: '验证码不能为空',
          position: 'top'
        })
        this.$refs.code.focus()
        return false
      }
      if (!/^\d{6}$/.test(code)) {
        this.$toast({
          message: '验证码格式错误',
          position: 'top'
        })
        this.$refs.code.focus()
        return false
      }
      return true
    },
    async onSendSms() {
      // 发送验证码
      try {
        await this.$refs['login-form'].validate('mobile')
        this.isSendSmsLoading = true // 点击之后让发送验证码按钮loading
        await sendSms(this.user.mobile)
        this.isCountDownShow = true // 请求发送后出现倒计时
      } catch (err) {
        let message = ''
        if (err && err.response && err.response.status === 429) {
          // 发送短信失败的错误提示
          message = '发送太频繁了，请稍后重试'
        } else if (err.name === 'mobile') {
          // 表单验证失败的错误提示
          message = err.message
        } else {
          // 未知错误
          message = '发送失败，请稍后重试'
        }
        this.$toast({
          message,
          position: 'top'
        })
      }
      // 无论成功或失败都不应该展示loading和倒计时
      this.isSendSmsLoading = false
      this.$isServer = false
    }
  }
}
</script>

<style scoped lang="less">
.login-container {
  .send-btn {
    width: 76px;
    height: 23px;
    background-color: #ededed;
    .van-button__text {
      font-size: 11px;
      color: #666666;
    }
  }
  .login-wrapper {
    padding: 26px 16px;
    .login-btn {
      background-color: #6db4fb;
      border: none;
      .van-button__text {
        font-size: 15px;
      }
    }
  }
}
</style>
