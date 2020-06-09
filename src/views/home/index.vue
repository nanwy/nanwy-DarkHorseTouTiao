<template>
  <div class="home-container">
    <!-- 导航栏 -->
     <div class="nav-bar">
      <div class="logo"></div>
      <van-button
        class="search-btn"
        round
        type="info"
        size="small"
        icon="search"
        @click="$router.push('/search')"
      >搜索</van-button>
    </div>
    <!-- /导航栏 -->

    <!-- 文章频道列表 -->
    <div class="channel-tabs">
      <van-tabs v-model="active" v-if="Object.keys(channels).length !== 0">
      <van-tab v-for="item in channels" :key="item.id" :title="item.name">{{item.name}}</van-tab>
    </van-tabs>
    </div>
    
    <!-- /文章频道列表 -->
  </div>
</template>

<script>
import {getUserChannels} from '@/api/user'
export default {
  name: 'HomeIndex',
  components: {},
  props: {},
  data () {
    return {
      active: 0, // 控制被激活的标签
      channels:[]
    }
  },
  computed: {},
  watch: {},
  created () {
    this.loadChannles()
  },
  mounted () {},
  methods: {
    async loadChannles(){
      const {data} = await getUserChannels()
      
      
      this.channels = data.data.channels
      console.log(data);
    }
  }
}
</script>

<style scoped lang="less">
.home-container {
  .nav-bar {
    position: fixed;
    top: 0;
    left: 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 15px;
    width: 100%;
    height: 46px;
    background-color: #3196fa;
    z-index: 1;
  .logo{
    background-image: url('./logo.png');
    background-size: cover;
    width: 100px;
    height: 30px;
  }
  }
  /deep/ .van-nav-bar__title {
    max-width: none;
  }
  .search-btn {
    width: 200px;
    height: 32px;
    background: #5babfb;
    border: none;
    .van-icon {
      font-size: 16px;
    }
    .van-button__text {
      font-size: 14px;
    }
  }
  
}
.channel-tabs {
  padding: 46px 0 50px;
  /deep/ .van-tab {
    border-right: 1px solid #edeff3;
    border-bottom: 1px solid #edeff3;
  }
  /deep/ .van-tabs__line {
    // padding: 46px 0 50px;
    bottom: 20px;
    width: 15px !important;
    height: 3px;
    background: #3296fa;
  }
}
</style>