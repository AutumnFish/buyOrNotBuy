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
    <!-- 内容区域 -->
    <div class="tab-content">
      <!-- <div v-show="selectIndex==1" class="content-item" v-html="goodsInfo.goods_introduce"> -->
      <div v-show="selectIndex==1" class="content-item">
        <wxParse :content="goodsInfo.goods_introduce" @preview="preview" @navigate="navigate" />
      </div>
      <div v-show="selectIndex==2" class="content-item">
        <!-- 根据属性 生成 左右的说明 -->
        <div class="row" v-for="(item, index) in goodsInfo.attrs" :key="item.goods_id">
          <div class="col">{{item.attr_name}}</div>
          <div class="col">{{item.attr_value}}</div>
        </div>

      </div>
    </div>

    <!-- 底部的操纵部分 -->
    <div class="bottom-box">
      <div class="item">
        <span class="iconfont icon-kefu" @click="callKefu"></span>
        联系客服
      </div>
      <div @click="toCart" class="item">
        <span class="iconfont icon-gouwuche"></span>
        购物车
      </div>
      <button @click="addToCart">加入购物车</button>
      <button>立即购买</button>
    </div>
  </div>
</template>

<script>
// 导入封装的tools
import tools from "../../utils/tools";
// 导入mpvue wxparse
import wxParse from "mpvue-wxparse";
export default {
  // 注册组件
  components: {
    wxParse
  },
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
    // 插件的方法1
    preview(src, e) {
      // do something
    },
    // 插件的方法2
    navigate(href, e) {
      // do something
    },
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
          // 缓存用户的地址
          wx.setStorage({
            key:'address',
            data:res
          })
        }
      });
    },
    // 切换tab
    changeTab(index) {
      this.selectIndex = index;
    },
    // 给客服打电话
    callKefu() {
      wx.makePhoneCall({
        phoneNumber: "10086" //仅为示例，并非真实的电话号码
      });
    },
    // 去购物车
    toCart() {
      // 有打印
      console.log("去购物车");
      // 代码跳转
      // 购物车页面使用的是 wx的 tabbar来进行配置的
      // tabbar里面的页面无法使用 navigateTo跳转
      // wx.navigateTo({ url: '/pages/cart/main' });
      wx.switchTab({ url: "/pages/cart/main" });
    },
    // 加入购物车
    addToCart() {
      // 读取购物车数据
      // 没有数据返回的是 空字符串
      let cartData = wx.getStorageSync("cartData");
      // console.log(cartData);
      // 如果为空 说明没有加入过
      if (cartData) {
        // 进入到这里 说明 有值
        // 判断是否有这个key
        if (cartData[this.goods_id]) {
          // 能到这 说明这个商品加入过
          cartData[this.goods_id] += 1;
        } else {
          // 没有加入过
          cartData[this.goods_id] = 1;
        }
      } else {
        // 到这里 说明没值
        // 因为默认是空字符串 变为对象才可以保存数据
        cartData = {};
        cartData[this.goods_id] = 1;
      }

      // 保存到缓存中
      // 插件问题 提示的是value 真实的是 data
      wx.setStorage({
        key: "cartData",
        data: cartData
      });

      // 使用异步读取的方式 逻辑编写会较为麻烦 因为 fail 跟success 都需要重新写入缓存
      // wx.getStorage({
      //   key: "cartData",
      //   success: res => {
      //     console.log(res.data)
      //     // cartData =
      //     // 判断购物车是否已有数据
      //     // if()
      //   },
      //   fail: () => {
      //     // 购物车还是空空如也
      //     // 为cartData赋值即可
      //     cartData = {};
      //     // 增加键值对即可
      //     cartData[this.goods_id] = 1;
      //   },
      //   complete: () => {}
      // });
    }
  },
  // mpvue开发的时候 Vue的生命周期钩子可以使用
  // 但是执行的时机 需要考虑小程序一些特殊情况
  created(options) {
    // console.log('created');
    // console.log(options);
    // console.log('被创建了');
  },
  // 在页面显示的时候 尝试获取地址
  onShow(){
    wx.getStorage({
      key: 'address',
      success: (res) => {
        // console.log(res.data)
        // 获取地址的值 设置到页面上即可
        this.address = res.data.provinceName+res.data.cityName+res.data.countyName;
      },
      fail: () => { 
        // 失败 什么都不用干

      },
      complete: () => { }
    })
  }
};
</script>

<style  lang="scss">
// 导入 wxparse的样式
@import url("~mpvue-wxparse/src/wxParse.css");
// 覆盖默认样式
.wxParse .p {
  margin: 0;
}
// 图片变快 去掉间隙
.wxParse .img {
  display: block;
}
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
  background-color: white;
  .content-item {
    .row {
      display: flex;
      justify-content: center;
      .col {
        border: 2rpx solid gray;
        // width: 180rpx;
        flex: 1;
        margin: 0 auto;
        font-size: 24rpx;
        color: gray;
        text-align: center;
        line-height: 40rpx;
        height: 40rpx;

        &.col:last-child {
          // margin-left: -2rpx;
          border-left: none;
        }
      }
      .col {
        border-bottom: none;
      }
      // 为最后一个row的col 加上底部边框
      &:last-child {
        .col {
          border-bottom: 2rpx solid gray;
        }
      }
    }
  }
}
</style>
