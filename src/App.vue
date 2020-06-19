<template>
  <div id="q-app">
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
body {
 background: rgb(55,73,146);
 background: linear-gradient(180deg, rgba(55,73,146,1) 0%, rgba(55,73,146,1) 54%, rgba(83,155,239,1) 100%);
 background-repeat: repeat, no-repeat;
 background-size: auto, 100% 100%;
 background-attachment: fixed;
 overflow-y: scroll;
}
</style>
