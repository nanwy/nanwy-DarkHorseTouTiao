<template>
  <div class="login-container">
    <!-- 导航栏 -->
    <van-nav-bar
      class="app-nav-bar"
      title="注册 / 登录"
      left-arrow
      @click-left="$router.back()"
    />
    <!-- /导航栏 -->

    <!-- 登录表单 -->
    <van-cell-group>
      <van-field
        v-model="user.mobile"
        icon-prefix="toutiao"
        left-icon="shouji"
        placeholder="请输入手机号"
      />
      <van-field
        v-model="user.code"
        clearable
        icon-prefix="toutiao"
        left-icon="yanzhengma"
        placeholder="请输入验证码"
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
          >发送验证码</van-button>
        </template>
      </van-field>
    </van-cell-group>
    <div class="login-btn-wrap">
      <van-button class="login-btn" type="info" block @click="onLogin">登录</van-button>
    </div>
    <!-- /登录表单 -->
  </div>
</template>

<script>
import { login,sendSms } from "@/api/user";
export default {
  name: "LoginIndex",
  components: {},
  props: {},
  data() {
    return {
      user: {
        mobile: "", // 手机号
        code: "", // 验证码,

      },
              isSendSmsLoading: false,
        isCountDownShow: false
    };
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {
    console.log(sendSms);
    
  },
  methods: {
  async onLogin () {
  // 开始转圈圈
  if(this.user.mobile.length < 11){
    this.$toast.fail('登录失败，手机号格式错误')

    return
  }
   if(this.user.code.length < 6 ){
        this.$toast.fail('验证码格式有误')
        return
      }

  this.$toast.loading({
    duration: 0, // 持续时间，0表示持续展示不停止
    forbidClick: true, // 是否禁止背景点击
    message: '登录中...' // 提示消息
  })

  try {
    const res = await login(this.user)
    console.log('登录成功', res)
    // 提示 success 或者 fail 的时候，会先把其它的 toast 先清除
    this.$toast.success('登录成功')
  } catch (err) {
    console.log('登录失败', err)
    this.$toast.fail('登录失败，手机号或验证码错误')
  }
},
    async onSendSms () {
     
  try {
    // 校验手机号码
    // await this.$refs['login-form'].validate('mobile')

    // 验证通过，请求发送验证码
    this.isSendSmsLoading = true // 展示按钮的 loading 状态，防止网络慢用户多次点击触发发送行为
    await sendSms(this.user.mobile)

    // 短信发出去了,显示倒计时，关闭发送按钮
   this.isCountDownShow = true

    // 倒计时结束 -> 隐藏倒计时，显示发送按钮（监视倒计时的 finish 事件处理）
  } catch (err) {
    // try 里面任何代码的错误都会进入 catch
    // 不同的错误需要有不同的提示，那就需要判断了
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

    // 提示用户
    this.$toast({
      message,
      position: 'top'
    })
  }

  // 无论发送验证码成功还是失败，最后都要关闭发送按钮的 loading 状态
  this.isSendSmsLoading = false

  // 发送失败，显示发送按钮，关闭倒计时
  this.isCountDownShow = false
}
  }
};
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
  .login-btn-wrap {
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