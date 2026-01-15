<template>
  <view 
    v-if="visible" 
    class="fixed inset-0 z-50 transition-colors duration-300"
    :style="{ backgroundColor: `rgba(0,0,0,${bgOpacity})` }"
    @touchmove.stop.prevent
  >
    
    <!-- Main Scroll View -->
    <scroll-view 
      scroll-y 
      class="w-full h-full relative"
      :show-scrollbar="false"
      :enhanced="true"
      :bounces="false"
    >
       <!-- Sticky Swiper -->
       <view 
         class="sticky top-0 w-full z-0" 
         :class="isClosing ? 'animate-fade-out' : 'animate-fade-in'"
         :style="{ height: swiperHeight + 'px' }"
       >
          <up-swiper
            :list="imageList"
            :height="swiperHeight + 'px'"
            :indicator="true"
            indicatorMode="dot"
            :circular="true"
            :autoplay="true"
            imgMode="aspectFill"
            bgColor="#000000"
            @click="handleSwiperClick"
          ></up-swiper>
          <!-- Gradient Overlay -->
          <view class="absolute inset-0 bg-gradient-to-b from-black/30 via-transparent to-transparent pointer-events-none"></view>
       </view>

       <!-- Content -->
       <view 
         class="relative z-10 pointer-events-auto" 
         :class="isClosing ? 'animate-slide-down-fade-out' : 'animate-slide-up-fade-in'"
         :style="{ marginTop: contentMarginTop + 'px' }"
       >
          <view :style="dragStyle">
            <view class="bg-white rounded-t-[40rpx] min-h-[60vh] pb-10 shadow-[0_-10px_40px_rgba(0,0,0,0.1)]">
            <!-- Drag Handle Indicator -->
            <view
              class="w-full flex justify-center pt-3 pb-1"
              @touchstart="onHandleTouchStart"
              @touchmove.stop.prevent="onHandleTouchMove"
              @touchend="onHandleTouchEnd"
              @touchcancel="onHandleTouchEnd"
            >
                <view class="w-10 h-1 bg-gray-300 rounded-full"></view>
            </view>

            <view class="px-6 pt-2">
                <!-- Title & Rating -->
                <view class="flex justify-between items-start mb-2">
                    <text class="text-2xl font-bold text-gray-900 leading-tight w-3/4">Pleasant shelter 32-L</text>
                    <view class="flex items-center bg-yellow-100 px-2 py-1 rounded-lg">
                        <u-icon name="star-fill" color="#f59e0b" size="14"></u-icon>
                        <text class="ml-1 text-sm font-bold text-yellow-700">4.8</text>
                    </view>
                </view>
                
                <!-- Address -->
                <text class="text-gray-500 text-sm mb-6 block">1097 Ranchview Dr. Richardson, 62639</text>

                <!-- Tags -->
                <view class="flex flex-wrap gap-3 mb-8">
                    <view v-for="(tag, index) in ['Kitchen', 'Bathroom', 'Garage']" :key="index" 
                        class="px-4 py-2 rounded-full text-sm font-medium transition-colors"
                        :class="index === 2 ? 'bg-blue-50 text-blue-600' : 'bg-gray-100 text-gray-500'">
                        {{ tag }}
                    </view>
                </view>

                <!-- Description -->
                <text class="text-gray-900 font-bold text-lg mb-3 block">Description</text>
                <text class="text-gray-500 text-sm leading-relaxed mb-8 block">
                    Pleasant shelter is an ideal apartment for one or two people. There is all the necessary equipment and furniture, for example, air conditioning, dishwasher, refrigerator, washing machine, and a large balcony with a view of the park. The location is perfect, close to the subway and shopping centers.
                </text>

                <!-- Owner -->
                <view class="flex items-center justify-between p-4 bg-gray-50 rounded-2xl mb-6">
                    <view class="flex items-center space-x-3">
                        <image :src="card.user?.avatar" class="w-12 h-12 rounded-full border-2 border-white shadow-sm" mode="aspectFill"></image>
                        <view>
                            <text class="text-xs text-gray-400 block mb-0.5">Owner</text>
                            <text class="text-gray-900 font-bold block">{{ card.user?.name || 'Unknown' }}</text>
                        </view>
                    </view>
                    <view class="flex space-x-3">
                        <view class="w-10 h-10 bg-blue-100 rounded-full flex items-center justify-center active:scale-95 transition-transform">
                            <u-icon name="phone-fill" color="#3b82f6" size="20"></u-icon>
                        </view>
                        <view class="w-10 h-10 bg-blue-500 rounded-full flex items-center justify-center shadow-lg shadow-blue-500/30 active:scale-95 transition-transform">
                            <u-icon name="chat-fill" color="#ffffff" size="20"></u-icon>
                        </view>
                    </view>
                </view>
                
                 <!-- Price & Action -->
                 <view class="pt-4 border-t border-gray-100 flex justify-between items-center">
                    <view>
                        <text class="text-sm text-gray-400">Price</text>
                        <view class="flex items-end">
                            <text class="text-2xl font-bold text-blue-600">$1,200</text>
                            <text class="text-sm text-gray-400 mb-1 ml-1">/month</text>
                        </view>
                    </view>
                    <button class="bg-blue-600 text-white px-8 py-3 rounded-xl font-bold text-sm shadow-lg shadow-blue-500/30 active:scale-95 border-none m-0">Rent Now</button>
                 </view>
            </view>
          </view>
          </view>
       </view>
    </scroll-view>

    <!-- Top Navigation (Fixed) -->
    <view class="fixed top-0 left-0 w-full z-20 pt-12 px-6 flex items-center gap-4 pointer-events-none animate-slide-down">
        <view class="w-10 h-10 bg-black/20 backdrop-blur-md rounded-full flex items-center justify-center pointer-events-auto active:scale-95 transition-transform" @click="handleClose">
            <u-icon name="arrow-left" color="#ffffff" size="20"></u-icon>
        </view>
        <view class="w-10 h-10 bg-black/20 backdrop-blur-md rounded-full flex items-center justify-center pointer-events-auto active:scale-95 transition-transform">
            <u-icon name="bookmark" color="#ffffff" size="20"></u-icon>
        </view>
    </view>
  </view>
</template>

<script setup>
import { ref, watch, computed } from 'vue';

const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },
  card: {
    type: Object,
    default: () => ({})
  }
});

const emit = defineEmits(['close']);

const visible = ref(false);
const isClosing = ref(false);
const dragY = ref(0);
const isDragging = ref(false);
const startY = ref(0);
const closeThreshold = 120;

// Calculate heights
const systemInfo = uni.getSystemInfoSync();
const windowHeight = systemInfo.windowHeight;
const swiperHeight = computed(() => windowHeight * 0.55);
// Ensure list is always an array
const imageList = computed(() => props.card.images || []);
// We want content to overlap the bottom of the swiper.
const contentMarginTop = computed(() => -1 * windowHeight * 0.10);

const dragStyle = computed(() => {
  return {
    transform: `translateY(${dragY.value}px)`,
    transition: isDragging.value ? 'none' : 'transform 0.45s cubic-bezier(0.16, 1, 0.3, 1)'
  };
});

const bgOpacity = computed(() => {
  if (isClosing.value) return 0;
  const maxDrag = 220;
  return Math.max(0, 1 - Math.min(dragY.value, maxDrag) / maxDrag);
});

watch(() => props.show, (val) => {
  if (val) {
    visible.value = true;
    isClosing.value = false;
    dragY.value = 0;
    isDragging.value = false;
  } else {
    isClosing.value = true;
    dragY.value = 0;
    isDragging.value = false;
    setTimeout(() => {
        visible.value = false;
        isClosing.value = false;
    }, 300);
  }
});

const onHandleTouchStart = (e) => {
  isDragging.value = true;
  startY.value = e.touches?.[0]?.clientY ?? 0;
  dragY.value = 0;
};

const onHandleTouchMove = (e) => {
  if (!isDragging.value) return;
  const currentY = e.touches?.[0]?.clientY ?? startY.value;
  const delta = currentY - startY.value;
  dragY.value = Math.max(0, delta);
};

const onHandleTouchEnd = () => {
  if (!isDragging.value) return;
  isDragging.value = false;
  if (dragY.value >= closeThreshold) {
    dragY.value = 0;
    emit('close');
    return;
  }
  dragY.value = 0;
};

const handleClose = () => {
  emit('close');
};

const handleSwiperClick = (index) => {
    console.log('Swiper clicked:', index);
};
</script>

<style scoped>
.animate-fade-in {
  animation: fadeIn 0.4s ease-out forwards;
}

.animate-fade-out {
  animation: fadeOut 0.3s ease-in forwards;
}

.animate-slide-up-fade-in {
  animation: slideUpFadeIn 0.4s cubic-bezier(0.25, 0.8, 0.5, 1) forwards;
}

.animate-slide-down-fade-out {
  animation: slideDownFadeOut 0.3s ease-in forwards;
}

.animate-slide-down {
  animation: slideDown 0.4s cubic-bezier(0.25, 0.8, 0.5, 1) forwards;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes fadeOut {
  from { opacity: 1; }
  to { opacity: 0; }
}

@keyframes slideUpFadeIn {
  from { 
    transform: translateY(100%);
    opacity: 0; 
  }
  to { 
    transform: translateY(0);
    opacity: 1; 
  }
}

@keyframes slideDownFadeOut {
  from { 
    transform: translateY(0);
    opacity: 1; 
  }
  to { 
    transform: translateY(100%);
    opacity: 0; 
  }
}

@keyframes slideDown {
  from {
    transform: translateY(-100%);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>
