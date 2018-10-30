<template>
        <!-- 搜索框 -->
    <div class="search-box">
      <input @click="toSearch" type="text" placeholder="搜索">
      <icon type="search" size="14">
      </icon>
    </div>
</template>
<script>
export default {
    methods:{
      toSearch(){
        // 代码跳转页面 编程式导航
        wx.navigateTo({ url: '/pages/search/main' });
      }
    }
}
</script>

<style scoped lang="scss" >
// 增加 scoped 让这个样式 该组件 独有
// 定义变量
$uRed: #ff2d4a;
// 设置顶部的padding
page {
  padding-top: 100rpx;
}
// 顶部的搜索栏
.search-box {
  height: 100rpx;
  position: fixed;
  z-index: 998;
  padding: 20rpx 16rpx;
  box-sizing: border-box;
  width: 100%;
  left: 0;
  top: 0;
  background-color: $uRed;
  input {
    height: 60rpx;
    width: 100%;
    color: #bbb;
    background-color: white;
    text-align: center;
    border-radius: 10rpx;
    font-size: 26rpx;
  }
  icon {
    top: 50%;
    left: 300rpx;
    transform: translateY(-50%);
    position: absolute;
  }
}
</style>


