<template>
  <q-layout view="hHh lpR fFr">

    <Navbar />



    <q-page-container>
      <router-view />
    </q-page-container>

    <Footer v-if="userGoogle" />

  </q-layout>
</template>

<script setup>
  import Navbar from 'src/components/Navbar.vue';
  import Footer from 'src/components/Footer.vue'
  import { inject } from '@vue/runtime-core';

  const userGoogle = inject("userGoogle");
</script>
