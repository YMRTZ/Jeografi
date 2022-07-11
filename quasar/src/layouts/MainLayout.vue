<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-toolbar-title> Jeografi </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { initializeApp } from 'firebase/app';
import { firebaseConfig } from '../../firebase-config.js';
const app = initializeApp(firebaseConfig);
export default defineComponent({
  name: 'MainLayout',

  setup() {
    // return {
    //   app,
    // };
  },
});
</script>
