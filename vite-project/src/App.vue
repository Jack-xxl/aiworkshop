<template>
  <div class="app-shell">
    <AppNav />
    <router-view />
  </div>
</template>

<script setup>
import AppNav from "./components/AppNav.vue";
</script>
