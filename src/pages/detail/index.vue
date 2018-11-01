<template>
  <div class="detail-container">
    <!-- 顶部轮播图 -->
    <swiper class='swiper' indicator-dots autoplay circular>
      <swiper-item v-for="(item, index) in goodsInfo.pics" :key="item.pics_id">
        <image @click="yulanImage(item.pics_big)" mode="widthFix" :src="item.pics_big"></image>
      </swiper-item>
    </swiper>
    <!-- 商品基本信息 -->
    <div class="info-box">
      <div class="top">
        ¥{{goodsInfo.goods_price}}
      </div>
      <div class="mid">
        <div class="left">{{goodsInfo.goods_name}}</div>
        <div class="right">
          <span class="iconfont icon-shoucang"></span>
          收藏
        </div>
      </div>
      <div class="bottom">
        快递:&nbsp;&nbsp; 免运费
      </div>
    </div>

    <!-- 详细内容 -->
    <div class="section">
      <div class="item">
        <span class="key">促销</span>
        <span class="info active">满300减30元</span>
      </div>
      <div class="item">
        <span class="key">促销</span>
        <span class="info">满300减30元</span>
      </div>
    </div>
    <div class="section">
      <div class="item" @click="getAddress">
        <span class="key">送至</span>
        <span class='info'>{{address}}</span>
      </div>
    </div>
    <!-- tab栏 -->
    <div class="tab-bar">
      <div @click="changeTab(1)" :class="selectIndex==1?'active':''" class="tab-item">图文介绍</div>
      <div @click="changeTab(2)" :class="selectIndex==2?'active':''" class="tab-item">规格参数</div>
    </div>
    <div class="tab-content">
      <div v-show="selectIndex==1" class="content-item">1</div>
      <div v-show="selectIndex==2" class="content-item">2</div>
    </div>

    <!-- 底部的操纵部分 -->
    <div class="bottom-box">
      <div class="item">
        <span class="iconfont icon-kefu"></span>
        联系客服
      </div>
      <div class="item">
        <span class="iconfont icon-gouwuche"></span>
        购物车
      </div>
      <button>加入购物车</button>
      <button>立即购买</button>
    </div>
  </div>
</template>

<script>
// 导入封装的tools
import tools from "../../utils/tools";
export default {
  data() {
    return {
      // 传递过来的id
      goods_id: 0,
      // 商品数据
      goodsInfo: {},
      // 准备一个图片地址数组
      previewImage: [],
      // 地址
      address: "",
      // 选中的索引
      selectIndex: 1
    };
  },
  // // 接收传递过来的数据
  onLoad(options) {
    // console.log(options);
    this.goods_id = options.id;
    // 发送请求 获取数据
    tools
      .myPro({
        url:
          tools.baseUrl + `api/public/v1/goods/detail?goods_id=${this.goods_id}`
      })
      .then(result => {
        // console.log(result);
        this.goodsInfo = result.data.message;
        // 准备一个图片数组
        this.goodsInfo.pics.forEach(v => {
          this.previewImage.push(v.pics_big);
        });
      });
  },

  methods: {
    // 预览图片
    yulanImage(current) {
      // 调用微信原生的api
      wx.previewImage({
        current, //当前图片地址
        urls: this.previewImage //需要预览的图片链接列表,
      });
    },
    // 获取地址
    getAddress() {
      // 调用了 选择地址方法
      // 模拟器中只能获取到 张三
      // 真机上可以获取到自己的地址 如果曾经在微信上填写过地址可以直接获取
      // 如果没有填写过地址 会让你输入地址 这个地址输入页不用我们写,微信已经做好了

      wx.chooseAddress({
        // 回调函数中 输入了信息
        // 使用箭头函数 绑定this
        success: res => {
          // console.log(res.userName);
          // console.log(res.postalCode);
          // console.log(res.provinceName);
          // console.log(res.cityName);
          // console.log(res.countyName);
          // console.log(res.detailInfo);
          // console.log(res.nationalCode);
          // console.log(res.telNumber);
          // 拼接省市区
          this.address = res.provinceName + res.cityName + res.countyName;
        }
      });
    },
    // 切换tab
    changeTab(index) {
      this.selectIndex = index;
    }
  },
  // mpvue开发的时候 Vue的生命周期钩子可以使用
  // 但是执行的时机 需要考虑小程序一些特殊情况
  created(options) {
    // console.log('created');
    // console.log(options);
  }
};
</script>

<style scoped lang="scss">
$uRed: #eb4450;
.detail-container {
  background-color: gray;
  padding-bottom: 100rpx;
}
.swiper {
  height: 715rpx;
  image {
    // height: 715rpx;
    width: 100%;
    display: block;
    margin: 0 auto;
  }
}
// 基本信息
.info-box {
  padding-left: 14rpx;
  margin-bottom: 10rpx;
  background: white;
  .top {
    height: 110rpx;
    line-height: 110rpx;
    color: $uRed;
  }
  .mid {
    display: flex;
    .left {
      font-size: 30rpx;
      // padding-right: 80rpx;
      margin-right: 80rpx;
    }
    .right {
      width: 140rpx;
      border-left: 2rpx solid #ccc;
      color: #ccc;
      text-align: center;
      font-size: 24rpx;
      .iconfont {
        display: block;
        margin: 0 auto;
      }
    }
  }
  .bottom {
    color: #ccc;
    font-size: 26rpx;
    height: 90rpx;
    line-height: 90rpx;
  }
}

//  详细内容布局
.section {
  margin-bottom: 10rpx;
  .item {
    padding-left: 10rpx;
    height: 66rpx;
    line-height: 66rpx;
    font-size: 22rpx;
    background-color: white;
    .key {
      padding-right: 26rpx;
    }
    .info {
      color: gray;
      &.active {
        color: $uRed;
      }
    }
  }
}
// 底部布局
.bottom-box {
  height: 100rpx;
  position: fixed;
  background-color: white;
  left: 0;
  bottom: 0;
  width: 100%;
  display: flex;
  .item {
    flex: 2;
    font-size: 28rpx;
    text-align: center;
    padding-top: 14rpx;
    color: gray;
    .iconfont {
      display: block;
      margin-bottom: 14rpx;
      margin: 0 auto;
    }
  }
  button {
    flex: 3;
    color: white;
    font-size: 28rpx;
    border-radius: 0;
    line-height: 100rpx;
    &:first-of-type {
      background-color: #f4b73f;
    }
    &:last-of-type {
      background-color: #eb4450;
    }
  }
}

// tab栏
.tab-bar {
  background-color: white;
  height: 65rpx;
  display: flex;
  .tab-item {
    flex: 1;
    height: 100%;
    box-sizing: border-box;
    font-size: 24rpx;
    line-height: 65rpx;
    text-align: center;
    &.active {
      color: $uRed;
      border-bottom: 4rpx solid $uRed;
    }
  }
}
.tab-content {
  .content-item {
  }
}
</style>
