<template>
  <div class="search-container">
    <!-- 搜索框 -->
    <div class="search-box">
      <icon type="search" size="16">
      </icon>
      <input type="text" v-model.trim="searchValue" @confirm="searchProduct" placeholder="请输入你想要的商品">
      <button size="mini" type="defult">
        取消
      </button>
    </div>
    <!-- 历史记录 -->
    <div v-show="searchResult.length==0" class="history-list">
      <div class="title-box">
        <span class="title">历史记录</span>
        <span class="iconfont icon-shanchu" @click="clearHistory"></span>
      </div>
      <div class="items">
        <div @click="changeSearch(item)" class="item" v-for="(item, index) in history" :key="index">
          {{item}}
        </div>
      </div>
    </div>
    <!-- 搜索的结果 -->
    <div v-show="searchResult.length!=0" class="result-box">
      <!-- 控制排序方式 -->
      <div class="control">
        <div @click="changeFilter(1)" class="item" :class="filterIndex==1?'active':''">综合</div>
        <div @click="changeFilter(2)" class="item" :class="filterIndex==2?'active':''">销量</div>
        <div @click="changeFilter(3)" class="item" :class="filterIndex==3?'active':''">
          <div class="font-box">价格</div>
          <div class="arrow-box">
            <span class="heifont hei-up" :class="isUp==true?'active':''"></span>
            <span class="heifont hei-down" :class="isUp!=true?'active':''"></span>

          </div>
        </div>
      </div>
      <!-- 结果列表 -->
      <scroll-view class="items" scroll-y scroll-with-animation>
        <a v-for="(item, index) in backArr" :key="item.goods_id" class="item" :href="'/pages/detail/main?id='+item.goods_id">
          <div class="left">
            <img :src="item.goods_small_logo" alt="">
          </div>
          <div class="right">
            <div class="title">{{item.goods_name}}</div>
            <div class="price">¥<span>{{item.goods_price}}</span>.00</div>
          </div>
        </a>
      </scroll-view>

    </div>

  </div>
</template>

<script>
// 导入函数
import tools from "../../utils/tools";
export default {
  data() {
    return {
      // 搜索记录
      history: [],
      // 搜索内容
      searchValue: "",
      // 搜索结果数组 原始数组
      searchResult: [],
      // 专门用来排序的数组 是原始数组的副本
      orderArr: [],
      // 增加标记的变量 筛选条件
      // 1:综合 2 销量 3: 价格
      filterIndex: 1,
      // 箭头是上还是下
      isUp: true
    };
  },

  methods: {
    // 搜索内容
    searchProduct() {
      // 非空判断
      if (this.searchValue == "") {
        wx.showToast({
          title: "哥们,写点啥呗", //提示的内容,
          icon: "none", //图标,
          duration: 2000, //延迟时间,
          mask: true, //显示透明蒙层，防止触摸穿透,
          success: res => {}
        });
        // 阻断代码执行
        return;
      }
      // 判断是否存在
      let index = this.history.indexOf(this.searchValue);
      // console.log(index);
      if (index != -1) {
        // splice 参数1 从哪开始删 参数2 删除几个
        this.history.splice(index, 1);
      }
      // 获取输入的内容
      // 保存到缓存中
      // 保存缓存数据 在数组开始的位置添加
      // console.log(this.history);
      // 如果长度超过了10 移除1个
      if (this.history.length > 9) {
        this.history.pop();
      }
      this.history.unshift(this.searchValue);
      // 保存缓存
      // vscode
      wx.setStorage({
        key: "history",
        data: this.history
      });
      // 调用接口 查询数据
      tools
        .myPro({
          url:
            tools.baseUrl +
            `api/public/v1/goods/search?query=${this.searchValue}`
        })
        .then(result => {
          // console.log(result);
          // 保存原始数组
          this.searchResult = result.data.message.goods;
          // 保存排序数组
          this.orderArr = this.searchResult.concat([]);
          console.log(this.searchResult === this.orderArr);
        });

      // 清空文本框
      this.searchValue = "";
    },
    // 修改搜索的内容
    changeSearch(item) {
      this.searchValue = item;
    },
    // 清除数据
    clearHistory() {
      // 页面中的数据缓存
      this.history = [];
      // 本地缓存
      wx.removeStorage({
        key: "history",
        success: res => {
          // 提示用户
          wx.showToast({
            title: "删除了哦", //提示的内容,
            icon: "success", //图标,
            duration: 2000, //延迟时间,
            mask: true, //显示透明蒙层，防止触摸穿透,
            success: res => {}
          });
        }
      });
    },
    // 切换筛选的索引
    changeFilter(index) {
      this.filterIndex = index;
      // 如果点击的是3
      // 再额外的切换 上下箭头的显示
      if (index == 3) {
        this.isUp = !this.isUp;
      }
    }
  },

  created() {
    // 读取缓存数据
    wx.getStorage({
      key: "history",
      success: res => {
        // console.log(res.data);
        // 赋值给 history即可
        this.history = res.data;
      },
      fail: () => {
        console.log("失败了");
        // 空数组即可
      },
      complete: () => {}
    });
  },
  // 计算属性
  computed: {
    // 根据筛选的条件返回不同的数组
    backArr() {
      // 根据筛选的条件返回不同的数组即可
      if (this.filterIndex == 1) {
        // 初始状态
        return this.searchResult;
      } else if (this.filterIndex == 2) {
        // 把排序数组 按照 销量排序 然后返回
        this.orderArr.sort(function(a, b) {
          // a跟b是 数组的每一项
          // 到底要如何排序 需要我们通过返回值的方式告诉他
          return a.goods_id - b.goods_id;
        });
        // sort执行完毕之后 排序完毕
        return this.orderArr;
      } else {
        //
        this.orderArr.sort((a, b) => {
          // a跟b是 数组的每一项
          // 到底要如何排序 需要我们通过返回值的方式告诉他
          if (this.isUp == true) {
            return a.goods_price > b.goods_price;
          } else {
            return a.goods_price < b.goods_price;
          }
        });
        // sort执行完毕之后 排序完毕
        return this.orderArr;
      }
      // return
    }
  }
};
</script>

<style  lang='scss'>
$uRed: #eb4450;
// page{
//   height: 100%;
// }
// .search-container{
//   display: flex;
//   flex-direction: column;
//   height: 100%;
// }

.search-box {
  height: 120rpx;
  padding: 30rpx 16rpx;
  display: flex;
  align-items: center;
  box-sizing: border-box;
  position: relative;
  background-color: #eeeeee;
  icon {
    position: absolute;
    left: 40rpx;
    top: 50%;
    transform: translateY(-50%);
  }
  input {
    flex: 1;
    margin-right: 16rpx;
    background-color: white;
    font-size: 24rpx;
    padding-left: 70rpx;
  }
  button {
    border: 2rpx solid #bfbfbf;
    background-color: #eeeeee;
  }
}

// 搜索历史
.history-list {
  padding: 0 16rpx;
  .title-box {
    display: flex;
    justify-content: space-between;
    padding: 18rpx 0;
    .title {
      font-size: 26rpx;
    }
    .iconfont {
      color: #cccccc;
    }
  }
  .items {
    display: flex;
    flex-wrap: wrap;

    .item {
      padding: 18rpx;
      background-color: #dddddd;
      font-size: 24rpx;
      margin-right: 20rpx;
      margin-bottom: 14rpx;
    }
  }
}

// 搜索结果
.result-box {
  flex: 1;
  .control {
    display: flex;
    height: 100rpx;
    border-bottom: 2rpx solid #ccc;
    .item {
      flex: 1;
      height: 100%;
      text-align: center;
      line-height: 100rpx;
      font-size: 26rpx;
      &.active {
        color: $uRed;
      }
    }
    .item:last-child {
      display: flex;
      justify-content: center;
      .heifont {
        display: block;
        height: 10rpx;
        width: 20rpx;
        color: #ccc;
        &.active {
          color: black;
        }
      }
    }
    .item:last-child.active {
      .heifont.active {
        color: $uRed;
      }
    }
  }
  // 结果列表
  .items {
    padding-left: 20rpx;
    height: 982rpx;
    .item {
      height: 260rpx;
      padding: 30rpx 0;
      box-sizing: border-box;
      display: flex;
      border-bottom: 2rpx solid hotpink;
      .left {
        display: flex;
        align-items: center;
        justify-content: center;
        ._img {
          width: 200rpx;
          height: 200rpx;
        }
      }
      .right {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        padding: 0 30rpx;
        .title {
          font-size: 26rpx;
          overflow: hidden;

          text-overflow: ellipsis;

          display: -webkit-box;

          -webkit-box-orient: vertical;

          -webkit-line-clamp: 2;
        }
        .price {
          color: $uRed;
          font-size: 22rpx;
          ._span {
            font-size: 35rpx;
          }
        }
      }
    }
  }
}
</style>

