<template>
  <div>
    <!-- 搜索框 -->
    <searchBar></searchBar>
    <!-- 左右布局容器 -->
    <div class="category-box">
      <div class="left">
        <scroll-view scroll-y scroll-with-animation @scroll="">
          <div @click="changeIndex(index)" class="item" :class="index==selectIndex?'active':''" v-for="(item, index) in categoryList" :key="item.cat_id">
            {{item.cat_name}}
          </div>
        </scroll-view>

      </div>
      <div class="right">
        <scroll-view scroll-y scroll-with-animation @scroll="">
          <!-- 顶部广告图 -->
          <a href="" class="title-img">
            <img mode="aspectFit" src="/static/icon/titleImage.png" alt="">
          </a>

          <!-- 底部小区域 -->
          <div class="section" v-for="(item, index) in categoryList[selectIndex].children" :key="item.cat_id">
            <div class="title">/&nbsp;&nbsp;&nbsp;&nbsp;{{item.cat_name}}&nbsp;&nbsp;&nbsp;&nbsp;/</div>
            <div class="items">
              <div v-for="(it, i) in item.children" :key="it.cat_id" class="item">
                <a href="">
                  <img mode="aspectFit" :src="'https://autumnfish.cn/wx/'+it.cat_icon" alt="">
                  <p>{{it.cat_name}}</p>
                </a>
              </div>
            </div>
          </div>
        </scroll-view>
      </div>
    </div>
  </div>
</template>

<script>
// 导入组件
import searchBar from "../../components/searchBar.vue";
// 导入tools工具函数
import tools from "../../utils/tools";

export default {
  data() {
    return {
      // 分类数据
      categoryList: [],
      // 记录点击的元素 索引
      selectIndex: 0
    };
  },
  // 注册组件
  components: {
    searchBar
  },

  methods: {
    // 点击切换
    changeIndex(index) {
      this.selectIndex = index;
    }
  },
  // 生命周期函数
  created() {
    // 获取分类数据
    tools
      .myPro({
        url: tools.baseUrl + "api/public/v1/categories"
      })
      .then(result => {
        // console.log(result);
        // 保存分类数据
        this.categoryList = result.data.message;
      });
  }
};
</script>

/
<style  lang="scss">
// scoped 增加之后会为当前这个页面的所有标签增加一个随机的类名
// 我们写的样式 会追加一个 这个类名 变为私有的样式
// 约束滚动的尺寸
page{
  height: 100%;
  // 怪异盒模型
  box-sizing: border-box; 
}
page>view{
  height: 100%;
}
$uRed: #eb4450;
// 底部区域
.category-box {
  display: flex;
  height: 100%;
  // 滚动盒子的高度
  scroll-view{
    height: 100%;
  }
  .left {
    width: 200rpx;
    .item {
      background-color: #f4f4f4;
      height: 100rpx;
      font-size: 26rpx;
      text-align: center;
      line-height: 100rpx;
      &.active {
        color: $uRed;
        background-color: white;
        position: relative;
        &::before {
          content: "";
          position: absolute;
          width: 8rpx;
          height: 60rpx;
          background-color: $uRed;
          left: 0;
          top: 20rpx;
        }
      }
    }
  }
  .right {
    flex: 1;
    padding: 16rpx;
    .title-img {
      display: block;
      width: 100%;
      ._img {
        display: block;
        width: 100%;
        height: 180rpx;
      }
    }
    .section {
      .title {
        text-align: center;
        font-size: 26rpx;
        margin-top: 20rpx;
      }
      .items {
        display: flex;
        flex-wrap: wrap;
        .item {
          width: 33.3333%;
          padding: 20rpx 0;
          ._a {
            display: block;
            ._img {
              display: block;
              width: 100rpx;
              height: 100rpx;
              margin: 0 auto;
            }
            ._p {
              text-align: center;
              font-size: 24rpx;
            }
          }
        }
      }
    }
  }
}
</style>
