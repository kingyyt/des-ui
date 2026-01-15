<template>
  <view 
    v-show="shouldRender"
    class="fixed inset-0 z-50 bg-[#0a1128] flex flex-col items-center pt-16 pb-10 px-6 transform ease-out transition-transform"
    :style="{ 
      transform: active ? 'translateY(0)' : 'translateY(-100%)',
      transitionDuration: '1000ms'
    }"
  >
    <!-- Top Left Icons -->
    <view class="absolute top-12 left-6 flex items-center space-x-4 z-50">
      <!-- Close Button -->
      <view 
        class="w-10 h-10 flex items-center justify-center bg-white/10 rounded-full active:bg-white/20 transition-colors backdrop-blur-sm" 
        @click="$emit('close')"
      >
         <u-icon name="arrow-up" color="#ffffff" size="20"></u-icon>
      </view>
      <!-- Settings Button -->
      <view 
        class="w-10 h-10 flex items-center justify-center bg-white/10 rounded-full active:bg-white/20 transition-colors backdrop-blur-sm"
      >
         <u-icon name="setting" color="#ffffff" size="20"></u-icon>
      </view>
    </view>

    <!-- Avatar -->
    <view class="mt-8 mb-4 relative">
       <u-image src="/static/img/1.png" width="100px" height="100px" shape="circle" border="2px solid rgba(255,255,255,0.1)"></u-image>
       <!-- Online indicator -->
       <view class="absolute bottom-1 right-1 w-4 h-4 bg-green-500 rounded-full border-2 border-[#0a1128]"></view>
    </view>

    <!-- Info -->
    <text class="text-2xl font-bold mb-2 tracking-wide text-white">Madelyn Saris</text>
    <text class="text-gray-400 text-xs mb-8 text-center max-w-[200px]">I love beautiful sunsets and my corgi, Charlie</text>

    <!-- QR Code Area -->
    <view class="bg-white p-4 rounded-2xl mb-10 shadow-xl shadow-white/5">
       <view class="relative w-[180px] h-[180px]">
          <u-image src="/static/img/2.png" width="100%" height="100%" radius="8px"></u-image>
          <view class="absolute inset-0 flex items-center justify-center mix-blend-hard-light bg-black/10">
              <u-icon name="scan" color="#000" size="60" class="opacity-50"></u-icon>
          </view>
       </view>
    </view>

    <!-- Share Button -->
    <button class="bg-black text-white rounded-full px-20 py-4 text-sm font-bold flex items-center shadow-lg shadow-black/30 active:scale-95 transition-transform mb-auto">
      <u-icon name="share" color="#fff" size="18" class="mr-2"></u-icon>
      Share Profile
    </button>

    <!-- Mask for BottomActionSheet -->
    <view 
      v-if="sheetExpanded"
      class="fixed inset-0 bg-black/20 backdrop-blur-lg z-40 transition-opacity duration-500"
      @click="sheetExpanded = false"
      @touchmove.stop.prevent
    ></view>

    <!-- Bottom Action Sheet -->
    <view 
      class="absolute inset-x-0 bottom-0 z-50 transition-all duration-1000 ease-out transform"
      :style="{ transitionDelay: active ? '1000ms' : '0ms' }"
      :class="active ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-8'"
    >
       <BottomActionSheet v-model="sheetExpanded" />
    </view>
  </view>
</template>

<script setup>
import { ref, watch } from 'vue';
import BottomActionSheet from './BottomActionSheet.vue';

const sheetExpanded = ref(false);

const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },
  animated: {
    type: Boolean,
    default: false
  }
});

defineEmits(['close']);

const shouldRender = ref(false);
const active = ref(false);
let timer = null;

watch(() => props.show, (newVal) => {
  if (timer) clearTimeout(timer);
  
  if (newVal) {
    shouldRender.value = true;
    // Small delay to ensure display:block is applied before transform change
    setTimeout(() => {
      active.value = true;
    }, 50);
  } else {
    active.value = false;
    // Wait for animation to finish before hiding
    timer = setTimeout(() => {
      shouldRender.value = false;
    }, 1000);
  }
});
</script>