<template>
  <div>
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
    <div v-show="searchResult.length!=0" class="history-list">
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
    <div v-show="searchResult.length==0" class="result-box">
      <div class="control">
        <div class="item active">综合</div>
        <div class="item">销量</div>
        <div class="item">
           <div class="font-box">价格</div> <div class="arrow-box">
            <span class="heifont hei-up active"></span>
            <span class="heifont hei-down"></span>

          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      // 搜索记录
      history: [],
      // 搜索内容
      searchValue: "",
      // 搜索结果数组
      searchResult: []
    };
  },

  methods: {
    searchProduct() {
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
      this.history.unshift(this.searchValue);
      // console.log(this.history);
      // 如果长度超过了10 移除1个
      if (this.history.length > 10) {
        this.history.shift();
      }

      // 保存缓存
      // vscode
      wx.setStorage({
        key: "history",
        data: this.history
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
  }
};
</script>

<style scoped lang='scss'>
$uRed: #eb4450;
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
  }
}
</style>

