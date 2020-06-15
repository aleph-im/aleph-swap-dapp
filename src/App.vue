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
 background-image: url("assets/img/overlay.png"), url("assets/img/bg.jpg");
 background-repeat: repeat, no-repeat;
 background-size: auto, 100% 100%;
 background-attachment: fixed;
 overflow-y: scroll;
}
</style>