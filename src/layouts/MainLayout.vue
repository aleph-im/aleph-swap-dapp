<template>
  <q-layout view="hhh lpR fff">
    <q-header class="bg-transparent text-black q-pa-sm">
      <q-toolbar>
        <q-toolbar-title class="logo">
            <img src="~/assets/img/logo.svg">
            <span class="q-ml-sm text-mono text-bold">
                swap.aleph.im
                <q-badge color="primary" transparent align="middle">beta</q-badge>
            </span>
        </q-toolbar-title>
        <q-space />
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>

    <q-footer class="bg-transparent text-black q-pa-sm">
      Copyright ©2020-present <a href="https://aleph.im/">aleph.im project</a>, all rights reserved. 
    </q-footer>

  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',

  components: {
  },

  data () {
    return {
    }
  }
}
</script>

<style lang="scss">
.logo {
  .q-badge {
    position: absolute;
    margin-top: -0.2rem;
    margin-left: -1rem;
  }
}


.content {
  max-width: 980px;
  margin: 0 auto;
  padding: 0 10px;
  word-wrap: normal;

  .title {
    font-size: 3rem;

    @media (max-width: $breakpoint-sm-max) {
      font-size: 4.4vw;
    }
  }
}

</style>