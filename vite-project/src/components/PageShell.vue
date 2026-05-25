<template>
  <div :class="wide ? 'page-wide' : 'page'">
    <router-link v-if="showBack" class="back-link animate-fade-up" to="/">← 返回首页</router-link>
    <header v-if="title" class="page-hero animate-fade-up delay-1">
      <h1 class="text-gradient">{{ title }}</h1>
      <p v-if="subtitle" class="lead">{{ subtitle }}</p>
    </header>
    <div class="page-body animate-fade-up delay-2">
      <slot />
    </div>
  </div>
</template>

<script setup>
defineProps({
  title: { type: String, default: "" },
  subtitle: { type: String, default: "" },
  showBack: { type: Boolean, default: true },
  wide: { type: Boolean, default: false },
});
</script>
