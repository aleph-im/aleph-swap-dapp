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
        return `https://nuls.world/addresses/${this.address}`
      else if (this.chain == 'ETH')
        return `https://etherscan.io/address/${this.address}`
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

span a {
    color: #374992;
    text-decoration: none;
    border-bottom: 1px solid #374992;
}
</style>
