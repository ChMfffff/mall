<template>
  <div class="goods">
    <goods-list-item v-for="item in goods" :goodsItem="item"></goods-list-item>
  </div>
</template>

<script>
import GoodsListItem from './GoodsListItem'

export default {
  name: 'GoodsList',
  props: {
    goods: {
      type: Array,
      dafeult() {
        return []
      }
    }
  },
  components: {
    GoodsListItem
  }
}
</script>

<style>

</style>
