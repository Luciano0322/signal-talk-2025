<script setup lang="ts">
import { computed, ref } from 'vue'

const props = withDefaults(defineProps<{
  /** 資源路徑：.gif / .png / .jpg / .webm / .mp4 都可 */
  src: string
  /** 下方說明文字（顯示在媒體上） */
  caption?: string
  /** 替代文字 */
  alt?: string
  /** 寬度：Tailwind 類或自訂，例如 'w-[600px]'、'w-2/3'、'w-full' */
  width?: string
  /** 是否顯示 replay 按鈕（對 GIF/影片皆可） */
  replayable?: boolean
  /** 影片選項（當 src 為影片時生效） */
  autoplay?: boolean
  loop?: boolean
  muted?: boolean
  /** Caption 類別（可覆寫字級/陰影等） */
  captionClass?: string
}>(), {
  width: 'w-[300px]',
  replayable: false,
  autoplay: true,
  loop: true,
  muted: true,
  captionClass: 'text-2xl'
})

const key = ref(0)
const loaded = ref(false)

const isVideo = computed(() => {
  const s = props.src.toLowerCase()
  return s.endsWith('.mp4') || s.endsWith('.webm') || s.includes('format=mp4') || s.includes('format=webm')
})

const videoType = computed(() => {
  if (props.src.toLowerCase().endsWith('.webm')) return 'video/webm'
  return 'video/mp4'
})

const widthClass = computed(() => props.width)

function onLoaded() {
  loaded.value = true
}
</script>

<template>
  <div class="relative inline-block select-none w-full">
    <img
      v-if="!isVideo"
      :key="key"
      :src="src"
      :alt="alt || caption || 'smart-gif'"
      :class="[
        'rounded-xl max-w-full mx-auto',
        widthClass,
        'transition-opacity duration-500',
        loaded ? 'opacity-100' : 'opacity-0'
      ]"
      @load="onLoaded"
    />

    <video
      v-else
      :key="`video-${key}`"
      :class="[
        'rounded-xl max-w-full mx-auto',
        widthClass,
        'transition-opacity duration-500',
        loaded ? 'opacity-100' : 'opacity-0'
      ]"
      :autoplay="autoplay"
      :loop="loop"
      :muted="muted"
      playsinline
      @loadeddata="onLoaded"
    >
      <source :src="src" :type="videoType" />
    </video>

    <!-- Caption -->
    <div
      v-if="caption"
      class="absolute bottom-4 left-1/2 -translate-x-1/2
             text-white text-center font-bold drop-shadow-lg
             px-3"
      :class="captionClass"
    >
      {{ caption }}
    </div>

  </div>
</template>
