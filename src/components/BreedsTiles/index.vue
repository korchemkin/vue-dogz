<template>
  <div class="tiles-wrap">
    <div class="tiles">
      <tile-item
        v-for="(item, index) in data" :key="getKey(index)"
        :item="item"
        :isBanner="withBanner && index === 0"
        :default-liked="allItemsLiked"
        @toggleLike="onToggleLike"
      />
    </div>

    <div v-if="showLoader" class="tiles-loader">
      <loader />
    </div>

    <transition name="fade">
      <div
        v-if="isToTopVisible"
        class="tiles-to-top"
        @click="scrollToTop"
      >
        <toTop />
      </div>
    </transition>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import TileItem from './item.vue'
import storage from '@/helpers/storage'
import loader from '@/assets/svg/loader.svg'
import toTop from '@/assets/svg/totop.svg'

export default {
  name: 'BreedsTiles',
  props: {
    data: {
      type: Array,
      required: true,
      default () {
        return []
      }
    },
    withBanner: {
      type: Boolean,
      required: false,
      default: false
    },
    allItemsLiked: {
      type: Boolean,
      required: false,
      default: false
    },
    showLoader: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  components: {
    TileItem,
    loader,
    toTop
  },
  data () {
    return {
      likedTiles: []
    }
  },
  computed: {
    ...mapState({
      isToTopVisible: state => state.isToTopVisible
    })
  },
  created () {
    if (this.allItemsLiked) {
      this.likedTiles = [].concat(this.data)
    }
  },
  methods: {
    addLiked (tile) {
      this.likedTiles.push(tile)
    },
    removeLiked (tile) {
      const index = this.likedTiles.findIndex(el => el.url === tile.url)
      this.likedTiles.splice(index, 1)
    },
    onToggleLike ({ liked, source }) {
      if (this.allItemsLiked || !liked) {
        this.removeLiked(source)
      } else {
        this.addLiked(source)
      }
      storage.setItem('favorites', this.likedTiles)
      this.$emit('toggleLike')
    },
    getKey (index) {
      return Date.now() * index
    },
    scrollToTop () {
      window.scrollTo(0, 0)
    }
  }
}
</script>

<style lang="scss" scoped>
.tiles {
  &-wrap {
    position: relative;
  }

  display: grid;
  justify-items: center;
  grid-template-columns: repeat(3, 1fr);
  column-gap: 30px;
  row-gap: 30px;
  padding: 0 60px 200px 60px;
  color: #FFFFFF;

  &-loader {
    padding: 80px;
    text-align: center;
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 100;
  }

  &-to-top {
    position: fixed;
    z-index: 1;
    cursor: pointer;
    right: 60px;
    bottom: 5%;
    width: 55px;
    height: 55px;
  }
}

@media (max-width:1080px) {
  .tiles {
    grid-template-columns: repeat(2, 1fr);
    padding-left: 30px;
    padding-right: 30px;
  }
}

@media (max-width: 800px) {
  .tiles {
    grid-template-columns: 1fr;
    padding-left: 10px;
    padding-right: 10px;
  }
}
</style>
