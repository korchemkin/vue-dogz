<template>
  <div class="favorites">
    <breeds-tiles :data="data" all-items-liked @toggleLike="getData" />
  </div>
</template>

<script>
import { createNamespacedHelpers } from 'vuex'
import breedsFavorites from '@/store/modules/breedsFavorites'

const { mapState, mapActions } = createNamespacedHelpers('breedsFavorites')
const BreedsTiles = () => import('@/components/BreedsTiles/')

export default {
  name: 'FavoritesView',
  components: {
    BreedsTiles
  },
  computed: {
    ...mapState({
      data: state => state.data
    })
  },
  created () {
    if (!this.$store.hasModule('breedsFavorites')) {
      this.$store.registerModule('breedsFavorites', breedsFavorites)
    }
    this.getData()
  },
  methods: {
    ...mapActions(['getData'])
  }
}
</script>

<style lang="scss" scoped>
.favorites {
  padding-top: 122px;
}

@media (max-width: 800px) {
  .favorites {
    padding-top: 30px;
  }
}
</style>
