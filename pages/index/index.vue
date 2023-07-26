<script setup lang="ts">
// get  /api/channel
// 通过 useFetch 获取频道列表数据，data 是响应式数据，可以直接用于界面渲染
const { data: channelList } = await useFetch('/api/channel')

// 获取视频列表数据
const { data: videoList } = await useFetch('/api/video')

// 显示的列表
const list = ref<any[]>([])
// 加载状态
const loading = ref(false)
// 是否加载完成
const finished = ref(false)

// 页码
let page = 1
let pageSize = 20

// 滚动触底触发
const onLoad = () => {
  // 表示正在加载
  loading.value = false
  // 根据当前页码提取数据
  const data = videoList.value?.slice(
    (page - 1) * pageSize,
    page * pageSize,
  ) as any[]
  // 追加到用于渲染的数组中
  list.value.push(...data)
  // 页码累加
  page++
  // 加载结束
  if (videoList.value?.length === list.value.length) {
    finished.value = true
  }
}

// 初始化加载 - 主动请求前 20 条数据，用于首屏渲染，方便 SEO 抓取到数据
onLoad()
</script>

<template>
  <!-- 公共头部 -->
  <header class="app-header">
    <NuxtLink class="logo" to="/">
      <i class="iconfont Navbar_logo"></i>
    </NuxtLink>
    <a class="search" href="#">
      <i class="iconfont ic_search_tab"></i>
    </a>
    <a class="face" href="#">
      <img src="@/assets/images/login.png" />
    </a>
    <div class="down-app">下载 APP</div>
  </header>
  <!-- 频道模块 -->
  <van-tabs>
    <van-tab v-for="item in channelList" :key="item.id" :title="item.name" />
  </van-tabs>
  <!-- 视频列表 -->
  <van-list
    v-model:loading="loading"
    :finished="finished"
    finished-text="去 bilibili App 看更多"
    @load="onLoad"
  >
    <div class="video-list">
      <NuxtLink
        class="v-card"
        v-for="item in list"
        :key="item.aid"
        :to="`/video`"
      >
        <div class="card">
          <div class="card-img">
            <img class="pic" :src="item.pic" :alt="item.title" />
          </div>
          <div class="count">
            <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              {{ item.stat.view }}
            </span>
            <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              {{ item.stat.danmaku }}
            </span>
          </div>
        </div>
        <p class="title">{{ item.title }}</p>
      </NuxtLink>
    </div>
  </van-list>
</template>

<style lang="scss">
// 公共头部
.app-header {
  display: flex;
  align-items: center;
  padding: 8px 12px;
  background-color: #fff;
  .logo {
    flex: 1;
    .Navbar_logo {
      color: #fb7299;
      font-size: 28px;
    }
  }
  .search {
    padding: 0 8px;
    .ic_search_tab {
      color: #ccc;
      font-size: 22px;
    }
  }
  .face {
    padding: 0 15px;
    img {
      width: 24px;
      height: 24px;
    }
  }
  .down-app {
    font-size: 12px;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #fb7299;
    color: #fff;
    border-radius: 5px;
    padding: 5px 10px;
  }
}

// 视频列表
.video-list {
  display: flex;
  flex-wrap: wrap;
  padding: 10px 5px;
}

// 视频卡片
.v-card {
  width: 50%;
  padding: 0 5px 10px;
  .card {
    position: relative;
    background: #f3f3f3 url(@/assets/images/default.png) center no-repeat;
    background-size: 36%;
    border-radius: 2px;
    overflow: hidden;
    .card-img {
      .pic {
        height: 100px;
        width: 100%;
        object-fit: cover;
      }
    }
    .count {
      background-image: linear-gradient(0deg, #000000d9, #0000);
      color: #fff;
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      font-size: 12px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 5px 6px;
      span {
        .iconfont {
          font-size: 12px;
        }
      }
    }
  }
  .title {
    margin-top: 5px;
    font-size: 12px;
    color: #212121;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
  }
}
</style>
