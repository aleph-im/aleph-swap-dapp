<template>
  <span>
    {{amount}}
    <span><small>{{symbol}}</small></span>
  </span>
</template>
<script>
export default {
  name: 'CurrencyAmount',
  props: ['amount', 'symbol']
}
</script>
