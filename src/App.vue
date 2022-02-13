<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import Header from '@/components/Header.vue'
</script>

<template>
  <Header></Header>
  <div class="container">
    <RouterView />
  </div>
</template>
