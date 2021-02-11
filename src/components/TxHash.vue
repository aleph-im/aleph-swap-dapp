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
        return `https://nulscan.io/transaction/info?hash=${this.hash}`
      else if (this.chain == 'ETH')
        return `https://etherscan.io/tx/${this.hash}`
      else if (this.chain == 'BSC')
        return `https://bscscan.com/tx/${this.hash}`
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
</style>