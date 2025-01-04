<script setup lang="ts">
import { ref, onMounted, onUnmounted, computed } from 'vue';

const images = [
  '/image1.jpg',
  '/image2.jpg',
  '/image3.jpg',
  '/image4.jpg',
  '/image5.jpg',
  '/image6.jpg',
  '/image7.jpg',
  '/image8.jpg',
];

// Add cloned images for smooth transition
const displayImages = computed(() => [...images, ...images.slice(0, 3)]);

const carouselRef = ref<HTMLDivElement | null>(null);
const isDragging = ref(false);
const startX = ref(0);
const scrollLeft = ref(0);
const autoScrollInterval = ref<number | null>(null);
const scrollSpeed = 1;

const startDragging = (e: MouseEvent | TouchEvent) => {
  isDragging.value = true;
  if (carouselRef.value) {
    // 当前鼠标点击、或者拖动的位置，距离屏幕左侧的距离
    // 下面这句话是为了兼容鼠标事件与触摸事件之间的差异
    const clientX = 'touches' in e ? e.touches[0].clientX : e.clientX;
    console.log('clientX', clientX)

    // 盒子距离左侧屏幕的距离carouselRef.value.offsetLeft
    startX.value = clientX - carouselRef.value.offsetLeft;
    console.log('carouselRef.value.offsetLeft', carouselRef.value.offsetLeft)

    scrollLeft.value = carouselRef.value.scrollLeft;

    console.log('scrollLeft.value', scrollLeft.value)
    stopAutoScroll();
  }
};

const stopDragging = () => {
  isDragging.value = false;
  startAutoScroll();
};

const drag = (e: MouseEvent | TouchEvent) => {
  if (!isDragging.value || !carouselRef.value) return;
  e.preventDefault();
  const clientX = 'touches' in e ? e.touches[0].clientX : e.clientX;
  const x = clientX - carouselRef.value.offsetLeft;
  const walk = (x - startX.value) * 2;
  carouselRef.value.scrollLeft = scrollLeft.value - walk;
};

const checkScroll = () => {
  if (!carouselRef.value || isDragging.value) return;
  
  const container = carouselRef.value;
  const imageWidth = 296; // width + gap
  const resetPoint = imageWidth * images.length;
  
  container.scrollLeft += scrollSpeed;

  // Smooth reset: when we reach the cloned section, quietly jump back to the original position
  if (container.scrollLeft >= resetPoint) {
    container.scrollLeft = container.scrollLeft % resetPoint;
  }
};

const startAutoScroll = () => {
  stopAutoScroll();
  autoScrollInterval.value = setInterval(checkScroll, 16) as unknown as number;
};

const stopAutoScroll = () => {
  if (autoScrollInterval.value) {
    clearInterval(autoScrollInterval.value);
    autoScrollInterval.value = null;
  }
};

onMounted(() => {
  startAutoScroll();
});

onUnmounted(() => {
  stopAutoScroll();
});
</script>

<template>
  <div
    ref="carouselRef"
    class="carousel"
    @mousedown="startDragging"
    @mousemove="drag"
    @mouseup="stopDragging"
    @mouseleave="stopDragging"
    @touchstart="startDragging"
    @touchmove="drag"
    @touchend="stopDragging"
  >
    <div class="carousel-content">
      <img
        v-for="(image, index) in displayImages"
        :key="`${index}-${image}`"
        :src="image"
        :alt="`Image ${(index % images.length) + 1}`"
        class="carousel-image"
      />
    </div>
  </div>
</template>

<style scoped>
.carousel {
  width: 100%;
  overflow-x: hidden;
  position: relative;
  touch-action: pan-y pinch-zoom;
}

.carousel-content {
  display: flex;
  gap: 16px;
  padding: 16px;
}

.carousel-image {
  flex: 0 0 auto;
  width: 280px;
  height: 200px;
  object-fit: cover;
  border-radius: 8px;
  user-select: none;
  border: 1px solid #535bf2;
}
</style>