<template>
  <div class="index_main" v-if="showMe">
    <div class="index_header">
      <Search @toSearchPage="toSearchPage"></Search>
    </div>

    <div class="sub_banner">
      <img src="../images/subBanner.jpg">
    </div>
    <el-tabs class="ban_tab_sel" v-model="activeName2" type="card" @tab-click="handleClick">
      <el-tab-pane class="ban_tab_sel_item" label="目的地" name="first">
        <div class="tab_wrap" slot="label">
          <svg class="icon">
            <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#arrow-left.6f6409e"></use>
          </svg>
          <span class="text">目的地</span>
        </div>
        <div class="el-tab-pane-con">
          <div class="el-tab-pane-con-item" style="width: calc((100% - 10px)/3);">
            南宁
          </div>
          <div class="el-tab-pane-con-item" style="width: calc((100% - 10px)/3);">
            南宁
          </div>
          <div class="el-tab-pane-con-item" style="width: calc((100% - 10px)/3);">
            南宁
          </div>
        </div>
      </el-tab-pane>
      <el-tab-pane class="ban_tab_sel_item" label="特价产品">
        <div class="tab_wrap" slot="label">
          <svg class="icon">
            <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#arrow-left.6f6409e"></use>
          </svg>
          <span class="text">特价产品</span>
        </div>
        <div class="el-tab-pane-con order_box">
          <router-link to="/product/s0001" class="order_one">
            <div class="order_img">
              <img src="../images/dingdan.jpg" alt="">
            </div>
            <div class="order_info">
              <header class="order_info_t">
                <div class="order_title">
                  【跟团游】全程无自费泰国6天   5晚至尊安心游！双岛出海＋三   合一夜秀表演
                </div>

              </header>
              <list class="order_info_s">
                <li class="order_info_s_item">
                  <span class="start">★</span>
                  <span class="text">【精选酒店】</span>
                  <span class="desc">全程升级入住四颗星酒店；</span>
                </li>
                <li class="order_info_s_item">
                  <span class="start">★</span>
                  <span class="text">【优良资质】</span>
                  <span class="desc">保证全程无购物；</span>
                </li>
                <li class="order_info_s_item">
                  <span class="start">★</span>
                  <span class="text">【贴心赠送】</span>
                  <span class="desc">价值50元旅游意外险；</span>
                </li>
              </list>
              <footer class="order_info_b">
                <span class="order_price">￥2798</span>
                <span class="order_price_text">起/人</span>
              </footer>
            </div>
          </router-link>
        </div>
      </el-tab-pane>
    </el-tabs>
    <!-- 撑开Fixednav挡住的位置 -->
    <div class="space"></div>
    <!-- 固定导航栏 -->
    <Fixednav></Fixednav>
  </div>
</template>

<script>
  import Fixednav from './small_components/Fixed_nav';
  import {mapGetters} from 'vuex';
  import Search from './Search';

  export default {
    name: 'homepage',
    data() {
      return {
        showMe: false, // 是否展示当前页面
        search_word: '', // 搜索框搜索词
        isLoadingMore: false,
        activeName2: 'first'
      };
    },
    mounted() {
      // 设置当前状态为加载中
      this.$store.dispatch('setLoading', true);
      // 设置当前标记为主页
      this.$store.dispatch('setWhichpage', 'homepage');
      // 模拟请求等待
      var time = Math.floor(Math.random() * 2000);
      console.log('模拟加载用时' + time);
      setTimeout(() => {
        // 页面显示
        this.$store.dispatch('setLoading', false);
        this.showMe = true;
      }, time);
      setTimeout(() => {
        window.addEventListener('scroll', this.dispatchLoad, false);
      }, 0);

      this.scrollText = this.swipeInfo[0].desc;
    },
    beforeDestroy() {
      window.removeEventListener('scroll', this.dispatchLoad, false);
    },
    computed: {
      ...mapGetters([
        'getFalseHotWord',
        'getFalseBussinessbrief' // 商家简略信息
      ])
    },
    methods: {
      handleClick(tab, event) {
        console.log(tab, event);
      },
      toSearchPage(e, search_text) {
        this.$router.push('/search/' + search_text);
      },
      // 加载更多
      loadMore() {
        // 大于十五条不加载
        if (this.getFalseBussinessbrief.length > 15) return;
        this.$store.dispatch('setLoading', true);
        if (!this.isLoadingMore) { // 是否加载中
          this.isLoadingMore = true;
          setTimeout(() => {
            this.$store.dispatch('setLoading', false);
            if (this.getFalseBussinessbrief.length <= 15) {
              this.$store.dispatch('setHomepageMore', [...this.getFalseBussinessbrief, ...(this.getFalseBussinessbrief).slice(0, 5)]);
              // console.log(this.getFalseBussinessbrief);
            }
            this.isLoadingMore = false;
          }, 1000);
        }
      },
      // 触发加载更多
      dispatchLoad() {
        var dscrollTop = document.body.scrollTop || document.documentElement.scrollTop;
        if (document.documentElement.offsetHeight <= (dscrollTop + window.innerHeight + 1)) {
          console.info('触发加载');
          this.loadMore();
        }
      }
    },
    components: {
      Fixednav,
      Search,
    }
  };
</script>

<style lang="less">
  @baseBlue: #03a4ea;
  .ban_tab_sel {
    margin-top: 2px;
    display: flex;
    div.el-tabs__header {
      border-bottom: none;
    }

    .el-tabs__content {
      flex: 1;
      margin-top: 1px;
      padding-left: 2px;
    }
    .el-tab-pane-con {
      display: flex;
      width: 100%;
      flex-wrap: wrap;
    }
    .el-tab-pane-con-item {
      font-size: 0.36rem;
      color: #585858;
      margin-bottom: 2px;
      border: 1px solid #b6b6b6;
      width: calc((100% - 12px) / 3);
      margin-left: 2px;
      text-align: center;
      height: 0.83rem;
      line-height: 0.83rem;
      &:nth-child(3n+1){
        margin-left: 0;
      }
    }

    .el-tabs__nav {
      display: flex;
      flex-direction: column;
      .el-tabs__nav-scroll {

      }

      div.el-tabs__item {
        border: 1px solid #b9b9b9;
        margin: 0 0 2px 0;
        background: #e5e5e5;
        color: #585858;
        height: 1.8rem;
        font-size: 0.37rem;
        width: 2.1rem;

        .tab_wrap {
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: center;
          height: 100%;
        }

        &.is-active {
          border: 1px solid #b9b9b9;
          background: #ffffff;
        }
        .icon {
          width: 0.6rem;
          height: 0.6rem;
        }
      }
    }
  }

  .index_main {
    width: 100%;
    overflow: hidden;
    .index_header {
      background: @baseBlue;
      box-sizing: border-box;
      overflow: hidden;
    }
    .space {
      width: 100%;
      height: 1.55rem;
    }
    .sub_banner {
      height: 3.6rem;
      img {
        width: auto;
        height: 100%;
      }
    }
  }


  .order_box{

  }
  .order_one{
    background:#fff;
    margin-bottom: .2rem;
    width: 100%;
    border: 1px solid #bcbcbc;
    box-sizing: border-box;
    padding: 4px;
    display: flex;
    .order_img{
      flex: none;
      width: 3.5rem;
      display: flex;
      img{
        width:100%;
       /* height:auto;*/
        height: 100%;
        align-items: center;
      }
    }
    .order_info_s_item{
      font-size: 0.20rem;
      font-weight: normal;
      margin-left: 0.2rem;
      .start{
        font-size: 0.16rem;
        color: #fc9a00;
      }
      .text{
        color: #fc9a00;
        margin-left: -0.1rem;
      }
      .desc{
        color: #585858;
        font-weight: bold;
      }
    }
    .order_info{
      margin-left: 10px;
      box-sizing:border-box;
      word-break: break-all;
      .order_info_t{
        .order_title{
          font-weight: bold;
          font-size: 0.26rem;
          line-height: 0.36rem;
          color: #585858;
          margin-bottom: 2px;
        }
        .order_time{
          font-size:.3rem;
          color:#999;
          line-height:.6rem;
        }
      }
      .order_info_b{
        display: flex;
        align-items: baseline;
        justify-content: 	flex-end;
        /*padding: 0.1rem 0 0.15rem 0;*/
        .order_price{
          font-size: 0.48rem;
          color: #ff4000;
          font-weight: bold;
          margin-right: 0.05rem;
        }
        .order_price_text{
          font-size: 0.22rem;
          color: #ff4000;
          font-weight: bold;
          vertical-align: baseline;
        }
      }
    }
  }


</style>
