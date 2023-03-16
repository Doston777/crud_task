<template>
  <q-layout view="hHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />

        <q-toolbar-title>
          Quasar App
        </q-toolbar-title>

        <div><q-btn flat round icon="fa-solid fa-arrow-right-from-bracket"></q-btn></div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
      content-class="bg-grey-1"
    >
      <q-list class="text-center">
        <q-item-label
          header
          class="text-grey-8"
        >
          Essential Links
        </q-item-label>
        <q-item>
          <router-link class="text-center text-none" to="/">Home</router-link>
        </q-item>
        <q-item>
          <router-link class="text-center text-none" to="/types">Product types</router-link>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>


export default {
  name: 'MainLayout',
  components: {
  },
  data () {
    return {
      leftDrawerOpen: false,
    }
  }
}
</script>

<style scoped>
  .text-none {
    text-decoration: none !important;
    color: black;
  }
</style>