<template>
  <div>
    <!-- 没有数据的盒子 -->
    <div v-show="cartData==undefined" class="empty">
      <span class="iconfont icon-gouwuche"></span>
      <p>你好没有添加任何商品</p>
    </div>
    <!-- 有数据的盒子 -->
    <div v-show="cartData!=undefined" class="full">
      <!-- 顶部的地址 -->
      <div class="address-box">
        <div class="item">
          <span class="person">收货人:&nbsp;&nbsp;&nbsp;&nbsp;{{person}}</span>
          <span class="mobile">{{mobile}}&nbsp;&nbsp;&nbsp;&nbsp;
            <span class="iconfont icon-jiantouyou"></span>
          </span>
        </div>
        <div class="item">
          <span>收获地址:&nbsp;&nbsp;{{address}}</span>
        </div>
        <!-- 底部的线型渐变 -->
        <div class="line"></div>
      </div>
      <!-- 中间的商品内容 -->
      <div class="good-list">
        <div class="title">
          <span class="iconfont icon-dianpu"></span> 优购生活馆
        </div>
        <div class="item" v-for="(item, index) in goodsList" :key="item.goods_id">
          <div class="left-box">
            <!-- 双向绑定 绑定的其实是 表单元素的什么 value属性 -->
            <!-- <input v-model="item.selected" color="#eb4450" type="radio"> -->
            <input @click="changeSelected(index)" :checked="item.selected" color="#eb4450" type="radio">
          </div>
          <div class="right-box">
            <div class="left">
              <a :href="'/pages/detail/main?id='+item.goods_id">
                <img :src="item.goods_small_logo" alt="">
              </a>
            </div>
            <div class="right">
              <div class="title">{{item.goods_name}}</div>
              <div class="bottom">
                <div class="price">¥<span>{{item.goods_price}}</span>.00</div>
                <div class="number-control">
                  <span @click="sub(index)">-</span>
                  <span>{{item.buyCount}}</span>
                  <span @click="add(index)">+</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- 底部的购买操纵 -->
      <div class="bottom-box">
        <div @click="checkAll" class="item">
          <input :checked="isCheckAll" type="radio">
          全选
        </div>
        <div class="item">
          <div class="top">
            合计: <span>¥<span class='price'>{{totalPrice}}</span>.00</span>
          </div>
          <div class="bottom">包含运费</div>
        </div>
        <button>结算({{totalNum}})</button>
      </div>

    </div>
  </div>
</template>

<script>
// 导入 工具函数
import tools from "../../utils/tools";

export default {
  data() {
    return {
      // 购物车数据
      cartData: undefined,
      // 收获数据
      person: "",
      mobile: "",
      address: "",
      // 商品数据
      goodsList: [],
      // 是否被全选
      isCheckAll:true
    };
  },
  computed:{
    totalPrice(){
      // 遍历找到所有被选中的 求和
      let totalPrice = 0;
      this.goodsList.forEach(v=>{
        if(v.selected==true){
          totalPrice+=(v.buyCount*v.goods_price);
        }
      })
      return totalPrice;
    },
    // 总个数
    totalNum(){
       // 遍历找到所有被选中的 求和
      let totalNum = 0;
      this.goodsList.forEach(v=>{
        if(v.selected==true){
          totalNum+=v.buyCount;
        }
      })
      return totalNum;
    }
  },
  methods: {
    // 切换选中状态
    changeSelected(index) {
      // 取反
      this.goodsList[index].selected = !this.goodsList[index].selected;
    },
    // 加减商品
    sub(index) {
      this.goodsList[index].buyCount--;
      // 递减之后 如果==0 删除数据
      if (this.goodsList[index].buyCount == 0) {
        // 删除当前商品
        // this.goodsList.splice(index,1);
        wx.showModal({
          title: "友情提示", //提示的标题,
          content: "真的要删?", //提示的内容,
          showCancel: true, //是否显示取消按钮,
          cancelText: "取消", //取消按钮的文字，默认为取消，最多 4 个字符,
          cancelColor: "#000000", //取消按钮的文字颜色,
          confirmText: "确定", //确定按钮的文字，默认为取消，最多 4 个字符,
          confirmColor: "#3CC51F", //确定按钮的文字颜色,
          success: res => {
            if (res.confirm) {
              // console.log('用户点击确定')
              this.goodsList.splice(index, 1);
            } else if (res.cancel) {
              // console.log('用户点击取消')
              this.goodsList[index].buyCount = 1;
            }
          }
        });
      }
    },
    add(index) {
      this.goodsList[index].buyCount++;
    },
    // 全选
    checkAll(){
      this.isCheckAll =!this.isCheckAll;
      // 设置每一项 跟全选的状态一致
      this.goodsList.forEach(v=>{
        v.selected = this.isCheckAll;
      })
    }
  },

  // 页面打开获取数据
  created() {
    // // 读取数据
    // wx.getStorage({
    //   key: 'cartData',
    //   success: (res) => {
    //     console.log('读取成功');
    //     console.log(res.data)
    //   },
    //   fail: () => {
    //     console.log('读取失败');
    //    },
    //   complete: () => { }
    // })
  },
  // // 测试生命周期函数
  // beforeMount(){
  //   console.log('beforeMount');
  // },
  // mounted() {
  //   console.log('mounted');
  // },
  // 页面显示
  onShow() {
    // 读取收获地址
    wx.getStorage({
      key: "address",
      success: res => {
        console.log(res.data);
        // 获取内容
        this.person = res.data.userName;
        this.mobile = res.data.telNumber;
        this.address =
          res.data.provinceName + res.data.cityName + res.data.countyName;
      },
      fail: () => {
        // 不用干什么
      },
      complete: () => {}
    });
    // console.log('显示出来啦');
    // // 读取数据
    wx.getStorage({
      key: "cartData",
      success: res => {
        console.log("读取成功");
        console.log(res.data);
        // 显示加入购物车的内容
        // 赋值
        this.cartData = res.data;
        // 字符串拼接 id1,id2,id3
        let ids = "";
        for (const key in this.cartData) {
          ids += key;
          ids += ",";
        }
        // 去掉尾部的逗号
        ids = ids.slice(0, -1);
        // 获取商品数据
        tools
          .myPro({
            url:
              tools.baseUrl + "api/public/v1/goods/goodslist?goods_ids=" + ids
          })
          .then(result => {
            // console.log(result);
            // 把购买数量保存进去
            result.data.message.forEach(v => {
              // 通过id 获取购物车数据中的购买数量 拼接为完整的数据
              v.buyCount = this.cartData[v.goods_id];
              // 默认被选中
              v.selected = true;
            });
            // 保存购买列表
            this.goodsList = result.data.message;
          });
      },
      fail: () => {
        // 没有
        console.log("读取失败");
      },
      complete: () => {}
    });
  }
};
</script>
<style scoped lang="scss">
// 购物车空的演示
.empty {
  .iconfont {
    display: block;
    margin: 100rpx auto 50rpx;
    width: 180rpx;
    height: 180rpx;
    border-radius: 50%;
    background-color: #ff2d4a;
    font-size: 130rpx;
    text-align: center;
    color: white;
    line-height: 180rpx;
  }
  ._p {
    font-size: 24rpx;
    text-align: center;
  }
}
.full {
  padding-bottom: 100rpx;
  .address-box {
    .item {
      height: 100rpx;
      line-height: 100rpx;
      display: flex;
      justify-content: space-between;
      padding-left: 20rpx;
      padding-right: 35rpx;
      font-size: 28rpx;
      span {
      }
    }
    // 渐变的线段
    .line {
      height: 20rpx;
      // 线性渐变
      // background-image: linear-gradient(-45deg,blue 40%,white 40%,white 50%,red 50%);
      background-image: linear-gradient(
        -45deg,
        #3982ed 22.5%,
        white 22.5%,
        white 27.5%,
        orange 27.5%,
        orange 62.5%,
        white 62.5%,
        white 67.5%,
        #3982ed 67.5%
      );
      background-size: 150rpx 20rpx;
    }
  }
  // 商品列表
  .good-list {
    > .title {
      height: 90rpx;
      padding-left: 30rpx;
      line-height: 90rpx;
      font-size: 30rpx;
      border-bottom: 2rpx solid gray;
      .iconfont {
      }
    }
    // 每一个小区域
    .item {
      display: flex;
      height: 205rpx;
      .left-box {
        width: 85rpx;
        display: flex;
        justify-content: center;
        align-items: center;
        ._input {
          color: #ff2d4a;
          transform: scale(0.8);
        }
      }
      .right-box {
        flex: 1;
        border-bottom: 2rpx solid gray;
        padding: 25rpx 0;
        display: flex;
        .left {
          ._a {
            display: block;
            width: 155rpx;
            height: 155rpx;
            image {
              display: block;
              width: 100%;
              height: 100%;
            }
          }
        }
        .right {
          display: flex;
          flex-direction: column;
          justify-content: space-between;
          padding: 0 20rpx;
          .title {
            font-size: 30rpx;
            overflow: hidden;
            text-overflow: ellipsis;
            display: -webkit-box;
            -webkit-box-orient: vertical;
            -webkit-line-clamp: 2;
          }
          .bottom {
            display: flex;
            justify-content: space-between;
            // flex-end 当前轴的 底部 flex-start 当前轴的开始
            align-items: flex-end;
            .price {
              color: #ff2d4a;
              font-size: 26rpx;
              ._span {
                font-size: 30rpx;
              }
            }
            .number-control {
              ._span {
                // padding: 20rpx;
                width: 60rpx;
                height: 50rpx;
                line-height: 50rpx;
                display: inline-block;
                font-size: 26rpx;
                text-align: center;
                &:first-child {
                  border: 2rpx solid #000;
                }
                &:last-child {
                  border: 2rpx solid #000;
                }
              }
            }
          }
        }
      }
    }
  }
  // 底部的控制区域
  .bottom-box{
    display: flex;
    height: 100rpx;
    background-color: white;
    position: fixed;
    left: 0;
    bottom: 0;
    width: 100%;
    .item{
      // 单选框
      font-size:30rpx;
      flex:1;
      &:first-child{
        line-height: 100rpx;
        padding-left: 25rpx;
      }
      &:nth-child(2){
        padding-top: 15rpx;
      }
      ._input{}
      .top{
        margin-bottom: 6rpx;
        >._span{
          color:#eb4450;
          font-size:24rpx;
          .price{
            font-size:30rpx;
          }
        }
      }
      .bottom{
        font-size:24rpx;
        color:#ccc;
      }
    }
    button{
      flex:1;
      background-color: #eb4450;
      color:white;
    }
  }
}
//
</style>
