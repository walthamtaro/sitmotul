<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <header class="container mx-auto text-center">
   
      <img src="@/assets/logoSitmotul.svg" alt="logotipo del sitmotul" class="w-20 mx-auto mt-7">
      <h1 class="font-bold text-xl">SITMOTUL</h1>
      <p class="font-medium text-s sr-only">Sistema Institucional de Tutoría del ITS Motul</p>
  </header>
  <RouterView />
</template>
