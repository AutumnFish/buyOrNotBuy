<template>
  <div class="mine-box">
    <div class="header">
      <div class="icon-box">
        <span class="iconfont icon-shezhi"></span>
        <button @getuserinfo="getUserInfo" open-type="getUserInfo">
        </button>
        <img :src="icon" alt="">
        <span class="iconfont icon-xiaoxi"></span>
      </div>
      <p class="name">{{userName}}</p>
    </div>
    <!-- 底部的内容 -->
    <div class="bottom-box">
      <div class='shop-box'>
        <div class="item">
          <p class="num">0</p>
          <p class="name">收藏的店铺</p>
        </div>
        <div class="item">
          <p class="num">0</p>
          <p class="name">收藏的商品</p>
        </div>
        <div class="item">
          <p class="num">0</p>
          <p class="name">关注的商品</p>
        </div>
        <div class="item">
          <p class="num">0</p>
          <p class="name">我的足迹</p>
        </div>
      </div>
      <!-- 订单区域 -->
      <div @click="toOrder" class="order-box">
        <div class="title">我的订单</div>
        <div class="order-list">
          <div class="item">
            <span class="iconfont icon-daifukuan"></span>
            <p>待付款</p>
          </div>
          <div class="item">
            <span class="iconfont icon-daishouhuo"></span>
            <p>待收货</p>
          </div>
          <div class="item">
            <span class="iconfont icon-tuikuan"></span>
            <p>退款/退货</p>
          </div>
          <div class="item">
            <span class="iconfont icon-dingdan"></span>
            <p>全部订单</p>
          </div>
        </div>
      </div>
      <!-- 选项区域 -->
      <div class="section">
        <div class="item">
          <span>收货地址管理</span>
          <span class='iconfont icon-jiantouyou'></span>
        </div>
      </div>
      <div class="section">
        <div class="item">
          <span>联系客服</span>
          <span>400-618-4000</span>
        </div>
        <div class="item">
          <span>意见反馈</span>
          <span class='iconfont icon-jiantouyou'></span>
        </div>
        <div class="item">
          <span>关于我们</span>
          <span class='iconfont icon-jiantouyou'></span>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
// 导入工具函数
import tools from "../../utils/tools";
export default {
  data() {
    return {
      // 头像
      icon: "/static/icon/loli.png",
      // 用户昵称
      userName: "登录/注册",
      // wx.login之后的code
      code: "",
      // 保存接口需要的数据
      encryptedData: "",
      iv: "",
      rawData: "",
      signature: ""
    };
  },
  methods: {
    // 获取用户信息
    // 第一次会弹出用户授权
    // 当用户允许之后 可以获取用户信息
    getUserInfo(res) {
      wx.login({
        success: res => {
          // console.log(res);
          // 状态码
          this.code = res.code;
          // 登录成功之后 获取用户的更为私密的信息
          wx.getUserInfo({
            // 是否带上登录态的信息
            withCredentials: true,
            success: res => {
              // console.log(res);
              // 登录必须的字段
              this.encryptedData = res.encryptedData;
              this.iv = res.iv;
              this.rawData = res.rawData;
              this.signature = res.signature;
              // 当前的用户名 还有用户的头像
              this.userName = res.userInfo.nickName;
              this.icon = res.userInfo.avatarUrl;
              // 调用自己的接口实现登录功能
              tools
                .myPro({
                  url: tools.baseUrl + "api/public/v1/users/wxlogin",
                  method: "post",
                  // 微信返回给我们的信息 发送到了自己的服务器
                  data: {
                    code: this.code,
                    encryptedData: this.encryptedData,
                    iv: this.iv,
                    rawData: this.rawData,
                    signature: this.signature
                  }
                })
                .then(result => {
                  // 第三方登录之后的用户信息
                  // console.log(result);
                  // 保存用户的token
                  wx.setStorage({
                    key: "token",
                    data: result.data.message.token
                  });
                });
            },
            fail: () => {},
            complete: () => {}
          });
        },
        fail: () => {},
        complete: () => {}
      });
    },
    // 去订单页
    toOrder(){
      // 
      wx.navigateTo({ url: '/pages/order/main' });
    }
  },

  created() {}
};
</script>

<style scoped lang="scss">
$uRed: #ff2d4a;
.mine-box {
  background-color: gray;
}
.header {
  height: 425rpx;
  background-color: $uRed;
  overflow: hidden;
  .icon-box {
    display: flex;
    margin-top: 130rpx;
    align-items: center;
    justify-content: center;
    .iconfont {
      color: white;
      font-size: 28rpx;
    }
    ._img {
      width: 130rpx;
      height: 130rpx;
      border-radius: 50%;
      border: 6rpx solid white;
      margin: 0 60rpx;
    }
    button {
      width: 130rpx;
      height: 130rpx;
      position: absolute;
      // background-color: $uRed;
      background-color: hotpink;
      // border:none;
      // outline: none;
      opacity: 0;
      // visibility: hidden;
    }
  }
  .name {
    text-align: center;
    color: white;
    font-size: 28rpx;
    margin-top: 18rpx;
  }
}
// 底部内容1
.bottom-box {
  width: 720rpx;
  margin: 0 auto;
  transform: translateY(-22rpx);
}
.shop-box {
  display: flex;
  align-items: center;
  background-color: white;
  height: 120rpx;
  .item {
    flex: 1;
    text-align: center;
    font-size: 24rpx;
    color: #666666;
    .num {
    }
    .name {
      margin-top: 14rpx;
    }
  }
}
// 订单盒子
.order-box {
  background-color: white;
  margin-top: 20rpx;
  .title {
    height: 85rpx;
    line-height: 85rpx;
    padding-left: 30rpx;
    font-size: 30rpx;
    border-bottom: 2rpx solid gray;
  }
  .order-list {
    display: flex;
    align-items: center;
    height: 170rpx;
    .item {
      flex: 1;
      text-align: center;
      .iconfont {
        color: $uRed;
        font-size: 50rpx;
        font-weight: 700;
        display: block;
      }
      ._p {
        font-size: 26rpx;
        margin-top: 25rpx;
      }
    }
  }
}
// 选项区域
.section{
  margin-top: 20rpx;
  background-color: white;
  .item{
    height: 86rpx;
    display: flex;
    justify-content: space-between;
    padding: 0 30rpx;
    align-items: center;
    font-size:30rpx;
    ._span{
      &:last-child{
        color:#666;
      }
    }
  }
  // item 从第二个开始 都有顶部的边框
  // n是从0开始 n=0 如果n+1 那么1可以取得到 所以用 2
  .item:nth-child(n+2){
    border-top: 2rpx solid yellowgreen;
  }
}
</style>
