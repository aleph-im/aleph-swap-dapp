<template>
  <span class="text-caption">
    <a :href="url">{{hash}}</a>
  </span>
</template>
<script>
export default {
  name: 'TxHash',
  props: ['hash', 'chain'],
  computed: {
    url() {
      if (this.chain == 'NULS2')
        return `https://nuls.world/transactions/${this.hash}`
      else if (this.chain == 'ETH')
        return `https://etherscan.io/tx/${this.hash}`
      else if (this.chain == 'NEO')
        return `https://neoscan.io/transaction/${this.hash}`
      else
        return null
    }
  }
}
</script>
<style scoped>
span {
    display: inline-block;
    max-width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
}
span a {
    color: #374992;
    text-decoration: none;
    border-bottom: 1px solid #374992;
}
</style>
