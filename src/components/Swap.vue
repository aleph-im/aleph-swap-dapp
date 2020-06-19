<template>
  <div>
    <div class="row">
      <div>{{source_chain}}</div>
      <div class="q-ml-sm q-mr-sm">
        <q-icon name="double_arrow" />
      </div>
      <div>{{target_chain}}</div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'Swap',
  props: [],
  components: {
  },
  data() {
    return {
      source_chain: 'NULS2',
      target_chain: 'ETH',
      source_chains: [
        'NULS2',
        'NEO'
      ],
      target_chains: [
        'NULS2',
        'ETH',
        'NEO'
      ]
    }
  }
}
</script>
<style scoped>
</style>
