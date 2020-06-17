<template>
  <span>
    {{amount}}
    <img src="~/assets/img/logo.svg" alt="ALEPH" style="height: 1em" v-if="symbol=='ALEPH'" />
    <span v-else>{{symbol}}</span>
  </span>
</template>
<script>
export default {
  name: 'CurrencyAmount',
  props: ['amount', 'symbol']
}
</script>