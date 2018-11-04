<template>
  <div class="order-container">
    <!-- tab-bar -->
    <div class="tab-bar">
      <div class="item" @click="changeIndex(1)" :class="selectIndex==1?'active':''">
        全部
      </div>
      <div class="item" @click="changeIndex(2)" :class="selectIndex==2?'active':''">
        待付款
      </div>
      <div class="item" @click="changeIndex(3)" :class="selectIndex==3?'active':''">
        已付款
      </div>
      <div class="item" @click="changeIndex(4)" :class="selectIndex==4?'active':''">
        退款/退货
      </div>
    </div>
    <!-- .tab-content -->
    <div class="tab-content">
      <!-- 根据订单的个数 也要遍历 -->
      <div class="items" v-show="selectIndex==1">
        <div v-for="(item, index) in orderList" :key="item.order_number" class="item">
          <!-- 根据商品的个数进行遍历 -->
          <div v-for="(it, i) in item.goods" :key="it.goods_id" class="top">
            <div class="left">
              <img :src="it.goods_small_logo" alt="">
            </div>
            <div class="right">
              <div class="title">{{it.goods_name}}</div>
              <div class="right-bottom">
                <div class="price">¥<span>{{it.goods_price}}</span>.00</div>
                <div class="numer">x{{it.goods_number}}</div>
              </div>
            </div>
          </div>
          <div class="mid">
            共{{item.total_count}}件商品 总计: ¥{{item.total_price}} (含运费0.00)
          </div>
          <div class="bottom">
            <span>
              订单号:{{item.order_number}}
            </span>
            <button>支付</button>
          </div>
        </div>
      </div>
      <div class="items" v-show="selectIndex==2">
        待付款
      </div>
      <div class="items" v-show="selectIndex==3">
        已付款
      </div>
      <div class="items" v-show="selectIndex==4">
        退款/退货
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
      // 索引
      selectIndex: 1,
      // 保存订单信息
      orderList: []
    };
  },
  methods: {
    // 切换索引
    changeIndex(index) {
      this.selectIndex = index;
    }
  },

  // 发请求获取订单数据
  // created 当前这个小程序项目创建的时候 就执行了
  created() {
    // // 获取缓存中的token
    // wx.getStorage({
    //   key: "token",
    //   success: res => {
    //     // console.log(res.data)
    //     // 调用接口
    //     tools
    //       .myPro({
    //         url: tools.baseUrl + "api/public/v1/my/orders/all?type=1",
    //         header: {
    //           Authorization: res.data
    //         }
    //       })
    //       .then(result => {
    //         console.log(result);
    //       });
    //   },
    //   fail: () => {},
    //   complete: () => {}
    // });
  },
  // 页面显示的时候才去获取数据
  onShow() {
    // 获取缓存中的token
    wx.getStorage({
      key: "token",
      success: res => {
        // console.log(res.data)
        // 调用接口
        tools
          .myPro({
            url: tools.baseUrl + "api/public/v1/my/orders/all?type=1",
            header: {
              Authorization: res.data
            }
          })
          .then(result => {
            // console.log(result);
            // 保存数据
            this.orderList = result.data.message.orders;
          });
      },
      fail: () => {},
      complete: () => {}
    });
  }
};
</script>

<style lang="scss" scoped>
$uRed: #eb4450;
.order-container {
  background-color: #f3f3f3;
}
.tab-bar {
  background-color: white;
  display: flex;
  align-items: center;
  height: 100rpx;
  box-shadow: 0 0 10rpx gray;
  .item {
    flex: 1;
    height: 100%;
    box-sizing: border-box;
    line-height: 100rpx;
    text-align: center;
    font-size: 32rpx;
    &.active {
      color: $uRed;
      border-bottom: 10rpx solid $uRed;
    }
  }
}
// 底部
.tab-content {
  background-color: white;
  margin-top: 30rpx;
  .items {
    padding: 0 20rpx;
    background-color: skyblue;
  }
  .item {
    margin-bottom: 20rpx;
    background: white;
    // 顶部
    .top {
      height: 260rpx;
      padding: 30rpx 0;
      box-sizing: border-box;
      display: flex;
      border-bottom: 2rpx solid #ccc;
      align-items: center;
      .left {
        ._img {
          width: 200rpx;
          display: block;
          height: 200rpx;
        }
      }
      .right {
        margin-left: 20rpx;
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        .title {
          font-size: 30rpx;
          overflow: hidden;
          text-overflow: ellipsis;
          display: -webkit-box;
          -webkit-box-orient: vertical;
          -webkit-line-clamp: 2;
        }
        .right-bottom {
          font-size: 24rpx;
          display: flex;
          justify-content: space-between;
          .price {
            color: $uRed;
            ._span {
              font-size: 32rpx;
            }
          }
          .numer {
          }
        }
      }
    }
    // 中部
    .mid{
      text-align: right;
      height: 86rpx;
      line-height: 86rpx;
      color:#666;
      font-size:24rpx;
    }
    .bottom{
      border-top: 2rpx solid gray;
      display: flex;
      align-items: center;
       height: 86rpx;
      line-height: 86rpx;
      color:#666;
      font-size:24rpx;
      justify-content: space-between;
      button{
        color:$uRed;
        font-size:24rpx;
        margin: 0;
        padding: 0;
        width: 160rpx;
        height: 60rpx;
        line-height: 60rpx;
      }
    }
  }
}
</style>
