<script setup lang="ts">
import ImageCarousel from './components/ImageCarousel.vue'
</script>

<template>
  <div class="app-container">
    <h1>Image Carousel</h1>
    <ImageCarousel />
  </div>
</template>

<style>
.app-container {
  width: 100%;
  max-width: 100vw;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}
</style>