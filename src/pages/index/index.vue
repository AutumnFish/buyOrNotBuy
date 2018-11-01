<template>
  <div class="index-container">
    <searchBar></searchBar>
    <!-- 轮播图 -->
    <swiper indicator-dots autoplay circular>
      <swiper-item v-for="(item, index) in swiperList" :key="index">
        <image mode="aspectFill" :src="item.image_src"></image>
      </swiper-item>
    </swiper>
    <!-- 分类区域 -->
    <div class="category-box">
      <a v-for="item in categoryList" :href="item.navigator_url">
        <img :src="item.image_src" alt="">
        <p>{{item.name}}</p>
      </a>
    </div>
    <!-- 楼层数据 -->
    <div class="floor-box">
      <div v-for="(item, index) in floorList" :key="index" class="section">
        <div class="title" >
          <img :src="item.floor_title.image_src" alt="">
          <span>
            {{item.floor_title.name}}
          </span>
        </div>
        <!-- 底部 -->
        <div class="bottom">
          <a href="" v-for="(it, i) in item.product_list" :key="i">
            <img :src="it.image_src" alt="">
          </a>
        </div>
      </div>
    </div>
    <!-- 我是底线 -->
    <div v-show="showLine" class="bottom-line">
      <span class="iconfont icon-xiao"></span>
        我是有底线的
    </div>
    <!-- 返回顶部 -->
    <div @click="toTop" v-show="showTop" class="back-top">
      <span class="iconfont icon-jiantoushang"></span>
      顶部
    </div>



  </div>
</template>

<script>
// 导入模块
import tools from "../../utils/tools";

// 导入自定义组件
import searchBar from "../../components/searchBar";
// console.log(tools);
export default {
  data() {
    return {
      // 轮播图数据
      swiperList: [],
      // 分类数据
      categoryList: [],
      // 楼层数据
      floorList: [],
      // 底线是否显示
      showLine:false,
      // 回到顶部是否显示
      showTop:false
    };
  },
  // 注册组件
  components:{
    // 注册
    searchBar
  },
  methods: {
    toTop(){
      // 滚到顶部
      wx.pageScrollTo({
        scrollTop:0
      })
    }
  },
  // 获取网络数据
  created() {
    // 创建promise对象
    let p1 = tools.myPro({
      url: tools.baseUrl + "api/public/v1/home/swiperdata"
    });
    // 分类数据
    let p2 = tools.myPro({
      url: tools.baseUrl + "api/public/v1/home/catitems"
    });
      let p3 = tools.myPro({
      url: tools.baseUrl + "api/public/v1/home/floordata"
    });
    // 使用Promiseall一次全部请求
    Promise.all([p1, p2,p3]).then(resultList => {
      // 轮播图数据
      this.swiperList = resultList[0].data.message;
      // 分类数据
      // console.log(resultList[1]);
      this.categoryList = resultList[1].data.message;
      // 楼层数据
      // console.log(resultList[2]);
      this.floorList = resultList[2].data.message;
    });
    // // 网络数据获取
    // tools.myPro({
    //   url:tools.baseUrl+'api/public/v1/home/swiperdata'
    // }).then(result=>{
    //   // console.log(result);
    //   this.swiperList = result.data.message;
    // })
  },
  // 触底事件
  onReachBottom(){
    // console.log('下面木有啦');
    // 切换显示
    this.showLine = true;
  },
  onPageScroll(obj){
    // console.log(obj);
    if(obj.scrollTop>667){
      // 显示
      this.showTop = true;
    }else{
      this.showTop = false;
    }
  }
};
</script>

<style scoped lang="scss">
// 定义变量
$uRed: #ff2d4a;

// 使用padding 把内容往下压
.index-container{
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
// 分类布局
.category-box {
  display: flex;
  background-color: white;
  padding: 24rpx 0 29rpx;
  ._a {
    text-align: center;
    flex: 1;
    ._img {
      display: block;
      width: 100rpx;
      height: 100rpx;
      border-radius: 50%;
      margin: 0 auto;
    }
    ._p {
      font-size: 24rpx;
      padding-top: 24rpx;
    }
  }
  ._a:nth-child(1) ._img {
    box-shadow: 0rpx 6rpx 6rpx #ff8f81;
  }
  a ._a:nth-child(2) ._img {
    box-shadow: 0rpx 6rpx 6rpx #1dc7ff;
  }
  ._a:nth-child(3) ._img {
    box-shadow: 0rpx 6rpx 6rpx #4ab8ef;
  }
  ._a:nth-child(4) ._img {
    box-shadow: 0rpx 6rpx 6rpx #369afd;
  }
}

// 楼层布局
.floor-box{
  .section{
    .title{
      height: 120rpx;
      font-size:48rpx;
      color:$uRed;
      position: relative;
      ._span{
        z-index: 998;
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
      }
      ._img{
        display: block;
        width: 100%;
        height:100%;
        left: 0;
        top: 0;
        position: absolute;
      }
    }
    .bottom{
      overflow: hidden;
      height: 500rpx;
      padding-top: 15rpx;
      padding-left: 10rpx;
      padding-right: 10rpx;
      ._a{
        width: 33.333%;
        float: left;
        height: 50%;
        box-sizing: border-box;
        padding: 5rpx;
        &:nth-child(1){
          height: 100%;
        }
        ._img{
          display: block;
          width:100%;
          height: 100%;
          border-radius: 10rpx;
        }
      }
    }
  }
}
.bottom-line{
  font-size:24rpx;
  color:#999;
  text-align: center;
  padding-top:24rpx;
  .iconfont{}
}
// 回到顶部
.back-top{
  text-align: center;
  width: 80rpx;
  height: 80rpx;
  border-radius: 50%;
  background-color: gray;
  position: fixed;
  font-size:26rpx;
  bottom: 0;
  right: 20rpx;
  color:white;
  .iconfont{
    display: block;
  }
}

// 设置轮播图图片
swiper image{
  width: 100%;
}
</style>
