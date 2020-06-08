<template>
  <div class="my-container">
    <!-- 登录时头像以及个人信息 -->
    <div class="my-detail" v-if="user">
      <div class="detail-top">
        <div class="my-info">
          <div class="my-avatar">
            <img :src="userInfo.photo" />
          </div>
          <div class="my-name">{{ userInfo.name }}</div>
        </div>
        <van-button class="update-btn" round>编辑资料</van-button>
      </div>
      <div class="detail-tool">
        <van-grid class="data-info" :border="false">
          <van-grid-item class="data-info-item">
            <div slot="text" class="text-wrap">
              <div class="count">{{ userInfo.art_count }}</div>
              <div class="text">头条</div>
            </div>
          </van-grid-item>
          <van-grid-item class="data-info-item">
            <div slot="text" class="text-wrap">
              <div class="count">{{ userInfo.follow_count }}</div>
              <div class="text">关注</div>
            </div>
          </van-grid-item>
          <van-grid-item class="data-info-item">
            <div slot="text" class="text-wrap">
              <div class="count">{{ userInfo.fans_count }}</div>
              <div class="text">粉丝</div>
            </div>
          </van-grid-item>
          <van-grid-item class="data-info-item">
            <div class="text-wrap" slot="text">
              <div class="count">{{ userInfo.like_count }}</div>
              <div class="text">获赞</div>
            </div>
          </van-grid-item>
        </van-grid>
      </div>
    </div>
    <!-- 没有登录时状态 -->
    <div class="not-login" v-else>
      <div @click="$router.push('/login')">

      </div>
      <div class="text">登录 / 注册</div>
    </div>
    <!-- 收藏 历史 -->
    <van-grid class="nav-grid mb-10" :column-num="2">
      <van-grid-item
        class="nav-grid-item"
        icon-prefix="toutiao"
        icon="shoucang"
        text="收藏"
      />
      <van-grid-item
        class="nav-grid-item"
        icon-prefix="toutiao"
        icon="lishi"
        text="历史"
      />
    </van-grid>

    <!-- 列表区域 -->
    <van-cell title="消息通知" is-link to="/" />
    <van-cell class="mb-10" title="小智同学" is-link to="/" />
    <van-cell
      class="logout-cell"
      title="退出登录"
      v-if="user"
      @click="onLogout"
    />
  </div>
</template>

<script>
import { mapState } from 'vuex'
import { getUserInfo } from '@/api/user'
export default {
  name: 'myIndex',
  data() {
    return {
      userInfo: {}
    }
  },
  created() {
    this.getCurrentUserInfo()
  },
  methods: {
    async getCurrentUserInfo() {
      const { data } = await getUserInfo()
      this.userInfo = data.data
    },
    onLogout() {
      // 退出登录
      this.$dialog
        .confirm({
          title: '退出提示',
          message: '确定要退出登录吗？'
        })
        .then(() => this.$store.commit('setUser', null))
    }
  },
  computed: {
    // 将vuex中数据映射到本地
    ...mapState(['user'])
  }
}
</script>

<style lang="less" scoped>
.my-detail {
  // background: url('../../assets/banner.png');
  background-size: cover;
  .detail-top {
    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: unset;
    height: 115px;
    padding: 38px 20px;
    .my-info {
      display: flex;
      align-items: center;
      .my-avatar {
        box-sizing: border-box;
        width: 66px;
        height: 66px;
        border: 1px solid #fff;
        border-radius: 50%;
        img {
          width: 100%;
          height: 100%;
          border-radius: 50%;
        }
      }
      .my-name {
        font-size: 15px;
        color: #fff;
        margin-left: 11px;
      }
    }
    .update-btn {
      height: 28px;
      font-size: 10px;
      color: #666;
    }
  }
  .data-info {
    .data-info-item {
      height: 65px;
      color: #fff;
      .text-wrap {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        .count {
          font-size: 18px;
        }
        .text {
          font-size: 11px;
        }
      }
    }
    /deep/ .van-grid-item__content {
      background-color: unset;
    }
  }
}

// 没有登录时样式
.not-login {
  height: 180px;
  // background: url('../../assets/banner.png');
  background-size: cover;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  .mobile {
    width: 66px;
    height: 66px;
  }
  .text {
    font-size: 14px;
    color: #fff;
  }
}

// 收藏 历史样式
/deep/ .nav-grid {
  .nav-grid-item {
    height: 70px;
    .toutiao {
      font-size: 22px;
    }
    .toutiao-shoucang {
      color: #eb5253;
    }
    .toutiao-lishi {
      color: #ff9d1d;
    }
    .van-grid-item__text {
      font-size: 14px;
      color: #333333;
    }
  }
}

.logout-cell {
  text-align: center;
  color: #d86262;
}

.mb-10 {
  margin-bottom: 10px;
}
</style>