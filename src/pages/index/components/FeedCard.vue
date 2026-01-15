<template>
  <view 
    class="bg-[#1e293b] rounded-[32rpx] p-5 shadow-2xl transform transition-all duration-500 hover:scale-[1.02] relative z-10 mt-6"
  >
    
    <!-- Card Header -->
    <view class="flex justify-between items-center mb-4">
      <view class="flex items-center space-x-3">
        <u-image :src="card.user.avatar" width="40px" height="40px" shape="circle" border="2px solid #3b82f6"></u-image>
        <view>
          <text class="text-white font-bold text-lg block leading-none">{{ card.user.name }}</text>
          <text class="text-gray-400 text-xs block mt-1">{{ card.user.handle }}</text>
        </view>
      </view>
    </view>

    <!-- Title & Description -->
    <view class="mb-4">
      <text v-if="card.title" class="text-white text-xl font-bold block mb-1">{{ card.title }}</text>
      <text v-if="card.description" class="text-gray-400 text-sm block overflow-hidden whitespace-nowrap text-ellipsis w-full">{{ card.description }}</text>
    </view>

    <!-- Image Grid -->
    <view class="rounded-2xl overflow-hidden" v-if="card.images && card.images.length > 0">
      
      <!-- 1 Image: Full -->
      <view v-if="displayImages.length === 1" class="w-full h-48">
        <u-image :src="displayImages[0]" width="100%" height="100%" mode="aspectFill" @click="onPreview(0)"></u-image>
      </view>

      <!-- 2 Images: Vertical Stack -->
      <view v-else-if="displayImages.length === 2" class="grid grid-rows-2 gap-2 h-64">
        <view class="w-full h-full rounded-xl overflow-hidden" v-for="(img, idx) in displayImages" :key="idx">
          <u-image :src="img" width="100%" height="100%" mode="aspectFill" @click="onPreview(idx)"></u-image>
        </view>
      </view>

      <!-- 3 Images: Horizontal Row -->
      <view v-else-if="displayImages.length === 3" class="grid grid-cols-3 gap-2 h-32">
        <view class="w-full h-full rounded-xl overflow-hidden" v-for="(img, idx) in displayImages" :key="idx">
          <u-image :src="img" width="100%" height="100%" mode="aspectFill" @click="onPreview(idx)"></u-image>
        </view>
      </view>

      <!-- 4 Images: 2x2 Grid -->
      <view v-else-if="displayImages.length === 4" class="grid grid-cols-2 grid-rows-2 gap-2 h-64">
        <view class="w-full h-full rounded-xl overflow-hidden" v-for="(img, idx) in displayImages" :key="idx">
          <u-image :src="img" width="100%" height="100%" mode="aspectFill" @click="onPreview(idx)"></u-image>
        </view>
      </view>

      <!-- 5+ Images: 1 Big Left (2 rows), 2 Right Stacked, 2 Bottom -->
      <view v-else class="grid grid-cols-2 grid-rows-3 gap-2 h-72">
        <!-- 1: Big Left (Span 2 rows) -->
        <view class="row-span-2 rounded-xl overflow-hidden">
          <u-image :src="displayImages[0]" width="100%" height="100%" mode="aspectFill" @click="onPreview(0)"></u-image>
        </view>
        
        <!-- 2, 3: Right Stacked -->
        <view class="rounded-xl overflow-hidden" v-for="(img, idx) in displayImages.slice(1, 3)" :key="`right-${idx}`">
          <u-image :src="img" width="100%" height="100%" mode="aspectFill" @click="onPreview(idx + 1)"></u-image>
        </view>

        <!-- 4, 5: Bottom Row -->
        <view class="rounded-xl overflow-hidden" v-for="(img, idx) in displayImages.slice(3, 5)" :key="`bottom-${idx}`">
          <u-image :src="img" width="100%" height="100%" mode="aspectFill" @click="onPreview(idx + 3)"></u-image>
        </view>
      </view>

    </view>

    <!-- Card Footer -->
    <view class="flex justify-between items-center mt-5">
      <view class="flex items-center space-x-4">
        <view class="flex items-center space-x-1">
          <u-icon name="heart" color="#94a3b8" size="20"></u-icon>
          <text class="text-gray-400 text-sm">{{ card.stats.likes }}</text>
        </view>
        <view class="flex items-center space-x-1">
          <u-icon name="chat" color="#94a3b8" size="20"></u-icon>
          <text class="text-gray-400 text-sm">{{ card.stats.comments }}</text>
        </view>
      </view>
      <view 
        class="w-10 h-10 bg-[#3b82f6] rounded-full flex items-center justify-center shadow-lg shadow-blue-500/50 active:scale-95 transition-transform"
        @click="$emit('detail', card)"
      >
        <u-icon name="arrow-right" color="#ffffff" size="20"></u-icon>
      </view>
    </view>
  </view>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  card: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(['preview', 'detail']);

// Ensure we only process up to 5 images for the layout logic, 
// but we might want to pass all images to preview? 
// The prompt says "超过5张图片的话仅显示前5张" (Only display top 5 if > 5).
const displayImages = computed(() => {
  if (!props.card.images) return [];
  // We return all images here so we can slice them in the template for the 5+ case
  // For 1-4 cases, we just use the length.
  return props.card.images;
});

const onPreview = (index) => {
  emit('preview', { card: props.card, index });
};
</script>

<style scoped>
/* Image mode fix for web/h5 */
.mode-aspect-fill {
  object-fit: cover;
}
</style>