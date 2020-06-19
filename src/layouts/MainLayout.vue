<template>
  <q-layout view="hhh lpR fff">
    <q-header class="bg-transparent text-white q-pa-sm">
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

    <q-footer class="bg-white text-center text-black q-pa-sm">
      <strong>Copyright ©{{year}} <a class="text-black" href="https://aleph.im/">aleph.im</a>, all rights reserved.</strong>
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
      year: new Date().getFullYear()
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
    opacity: 1;
    color: white;
  }
}


.content {
  max-width: 980px;
  margin: 0 auto;
  padding: 0 10px;
  word-wrap: normal;
  color: white;

  .title {
    font-size: 3rem;

    @media (max-width: $breakpoint-sm-max) {
      font-size: 4.4vw;
    }
  }
}

</style>
