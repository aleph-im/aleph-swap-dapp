<template>
  <span class="text-caption">
    <a :href="url">{{address}}</a>
  </span>
</template>
<script>
export default {
  name: 'Address',
  props: ['address', 'chain'],
  computed: {
    url() {
      if (this.chain == 'NULS2')
        return `https://nulscan.io/address/info?address=${this.address}`
      else if (this.chain == 'ETH')
        return `https://etherscan.io/address/${this.address}`
      else if (this.chain == 'BSC')
        return `https://bscscan.com/address/${this.address}`
      else if (this.chain == 'NEO')
        return `https://neoscan.io/address/${this.address}`
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