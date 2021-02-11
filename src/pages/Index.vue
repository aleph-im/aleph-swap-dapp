<template>
  <q-page class="">
    <div class="content">
      <h4 class="text-white">
        Swap
      </h4>
        <swap />
      <h4 class="text-white row justify-between">
        History
        <q-btn
          color="white"
          round
          flat
          dense
          :icon="refreshing ? 'wait' : 'refresh'"
          :active="!refreshing"
          @click="get_swaps"
        />
      </h4>
      <div v-for="swap of swaps" :key="swap.original_item_hash">
        <swap-detail :swap="swap" />
      </div>
    </div>
  </q-page>
</template>

<script>
import Swap from '../components/Swap'
import SwapDetail from '../components/SwapDetail'
import { mapState } from 'vuex'
import { posts } from 'aleph-js'
export default {
  name: 'PageIndex',
  computed: {
    ...mapState([
      'api_server',
      'swap_address'
    ])
  },
  data() {
    return {
      swaps: [],
      refreshing: false
    }
  },
  components: {
    Swap,
    SwapDetail
  },
  methods: {
    async get_swaps() {
      this.refreshing = true
      let result = await posts.get_posts('xchain-swap', {
        addresses: [this.swap_address],
        api_server: this.api_server
      })
      console.log(result)
      this.swaps = result.posts
      this.refreshing = false
    }
  },
  mounted() {
    this.get_swaps()
  }
}
</script>
