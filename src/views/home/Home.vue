<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <scroll class="content"
            ref="scroll"
            :probeType="3"
            :pullUpLoad="true"
            @scroll="contentScroll"
            @pullingUp="loadMore"
    >
      <home-swiper :banners="banners"></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <feature-view/>
      <tab-control  class="tab-control"
                    :titles="['流行', '新款', '精选']"
                    @tabClick="tabClick"/>
      <goods-list :goods="showGoods"/>
    </scroll>
    <back-top @click.native="topClick" v-show="isShowBackTop"/>
  </div>
</template>

<script>
  //导入的公共组件
  import NavBar from 'components/common/navbar/NavBar'
  import TabControl from 'components/content/tabControl/TabControl'
  import GoodsList from 'components/content/goods/GoodsList'
  import Scroll from 'components/common/scroll/Scroll'
  import BackTop from 'components/content/backTop/BackTop'

  //导入的home的子组件
  import HomeSwiper from './childComp/HomeSwiper'
  import RecommendView from './childComp/RecommendView'
  import FeatureView from './childComp/FeatureView'

  //导入数据请求的方法
  import {getHomeMultidata, getHomeGoods} from 'network/home'

  export default {
    name: "Home",
    data() {
      return {
        banners: [],
        recommends: [],
        goods: {
          'pop': {page: 0, list: []},
          'new': {page: 0, list: []},
          'sell': {page: 0, list: []}
        },
        currentType: 'pop',
        isShowBackTop: false
      }
    },
    components: {
      NavBar,
      TabControl,
      GoodsList,
      BackTop,
      Scroll,
      HomeSwiper,
      RecommendView,
      FeatureView,
    },
    created() {
      this.getHomeMultidata();
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')
    },
    computed: {
      //通过计算属性监视currentType的值，然后传入goodslist组件进行显示
      showGoods() {
        return this.goods[this.currentType].list
      }
    },
    methods: {
      //事件监听相关的方法
      tabClick(index) {
        this.currentType = Object.keys(this.goods)[index]
      },
      //数据请求相关的方法
      getHomeMultidata(){
        getHomeMultidata().then(res => {
          this.banners = res.data.banner.list;
          this.recommends = res.data.recommend.list;
        })
      },
      getHomeGoods(type) {
        const page = this.goods[type].page + 1
        getHomeGoods(type, page).then(res => {
          this.goods[type].list.push(...res.data.list)
          this.goods[type].page += 1

          // 数据请求完成后重置下拉刷新
          this.$refs.scroll.finishPullUp()
        })
      },
      topClick() {
        this.$refs.scroll.scrollTo(0, 0)
      },
      //接受scroll子组件传过来的数据然后进行判断BackTop是否要展示
      contentScroll(position) {
        this.isShowBackTop = position.y < -1177
      },
      //scroll子组件触发了上拉加载更多后定义的事件
      loadMore() {
        //
        this.getHomeGoods(this.currentType)
      }
    }
  }
</script>

<style scoped>
  .home-nav {
    background-color: var(--color-tint);
    color: #fff;
    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 9;
  }
  #home {
    padding-top: 44px;
    height: 100vh;
    position: relative;
    overflow: hidden;
  }
  .tab-control {
    position: sticky;
    top: 44px;
    z-index: 9;
  }
  .content {
    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }
</style>
