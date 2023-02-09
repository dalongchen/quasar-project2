<template>
  <q-layout view="lHh Lpr lFf">
    <q-header v-model="drawerOpen">
      <q-toolbar>
        <q-btn flat dense round icon="menu" aria-label="Menu" @click="toggleLeftDrawer" />
        <q-toolbar-title>
          stock
        </q-toolbar-title>
        <div>Stock v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered :width="150" :breakpoint="1500">
      <q-list>
        <q-item-label header>
          Essential
        </q-item-label>
        <EssentialLink v-for="link in essentialLinks" :key="link.title" v-bind="link" />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { defineComponent, ref, provide } from 'vue';
import EssentialLink from 'components/EssentialLink.vue';


const linksList = [
  {
    title: 'tab',
    caption: 'quasar',
    icon: 'school',
    link: 'http://localhost:9000/#/tab'
  },
  {
    title: 'vote',
    caption: 'github',
    icon: 'code',
    link: 'http://localhost:9000/#/polls/5/vote/'
  },
  {
    title: 'mytest',
    caption: 'chat',
    icon: 'chat',
    link: 'http://localhost:9000/#/mytest'
  },
  {
    title: 'stockYjbbEm',
    caption: 'forum',
    icon: 'record_voice_over',
    link: 'http://localhost:9000/#/polls/stockYjbbEm/'
  },
  {
    title: 'zhangTing',
    caption: '@quasarframework',
    icon: 'rss_feed',
    link: 'http://localhost:9000/#/polls/zhangTing/'
  },
  {
    title: 'Facebook',
    caption: '@QuasarFramework',
    icon: 'public',
    link: 'https://facebook.quasar.dev'
  },
  {
    title: 'Quasar Awesome',
    caption: 'Community',
    icon: 'favorite',
    link: 'https://awesome.quasar.dev'
  }
];

export default defineComponent({
  name: 'MainLayoutSt',

  components: {
    EssentialLink
  },

  setup() {
    const leftDrawerOpen = ref(false)
    const drawerOpen = ref(false)
    function toggleLeftDrawer() {
      leftDrawerOpen.value = !leftDrawerOpen.value
    }
    function toggleDrawer() {
      drawerOpen.value = !drawerOpen.value
    }
    provide('toggleDrawer', toggleDrawer)

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      drawerOpen,
      toggleLeftDrawer
    }
  }
});
</script>
