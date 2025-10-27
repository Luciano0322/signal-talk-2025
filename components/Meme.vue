<script setup lang="ts">
defineProps<{ img: string; caption?: string }>()
</script>

<template>
  <div class="relative inline-block w-full">
    <img :src="img" class="rounded-xl w-[300px] mx-auto" />
    <div
      class="absolute bottom-6 left-1/2 -translate-x-1/2 text-white text-2xl font-bold drop-shadow-lg text-center"
    >
      {{ caption }}
    </div>
  </div>
</template>
