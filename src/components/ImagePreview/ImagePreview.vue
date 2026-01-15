<template>
  <view 
    v-if="show" 
    class="fixed inset-0 z-50 flex flex-col overflow-hidden perspective-1000"
    :style="maskStyle"
    @click="handleClose"
    @touchmove.stop.prevent
  >
    <!-- Main Container with Flip Animation -->
    <view 
      class="w-full h-full relative"
      :class="{ 'animate-flip-in': isAnimating, 'animate-flip-out': isClosing }"
    >
      <!-- Top Bar (Fade In Down) -->
      <view 
        class="absolute top-0 left-0 right-0 z-20 pt-12 px-6 flex items-center gap-4 animate-fade-in-down bg-gradient-to-b from-black/50 to-transparent pb-10 transition-opacity duration-200"
        :style="{ opacity: headerOpacity }"
        @click.stop
      >
        <!-- Back Icon -->
        <view 
          class="w-10 h-10 bg-white/10 backdrop-blur-md rounded-full flex items-center justify-center active:scale-95 transition-transform"
          @click="handleClose"
        >
          <u-icon name="arrow-left" color="#ffffff" size="20"></u-icon>
        </view>
        
        <!-- Save Button (Moved to left) -->
        <view 
          class="w-10 h-10 bg-white/10 backdrop-blur-md rounded-full flex items-center justify-center active:scale-95 transition-transform"
          @click="handleSave"
        >
          <u-icon name="download" color="#ffffff" size="20"></u-icon>
        </view>
      </view>

      <!-- Image Swiper -->
      <swiper 
        class="w-full h-full" 
        :current="currentIndex" 
        @change="onSwiperChange"
        :circular="true"
        :indicator-dots="false"
        :duration="300"
        :disable-touch="isDragging"
      >
        <swiper-item 
          v-for="(img, index) in images" 
          :key="index"
          class="flex items-center justify-center"
        >
          <view 
            class="w-full h-full flex items-center justify-center px-6"
            @touchstart="handleTouchStart"
            @touchmove="handleTouchMove"
            @touchend="handleTouchEnd"
          >
            <image 
              :src="img" 
              mode="aspectFit" 
              class="w-full h-full transition-transform duration-0"
              :style="index === currentIndex ? imageStyle : ''"
              @click.stop
            ></image>
          </view>
        </swiper-item>
      </swiper>
      
      <!-- Page Indicator -->
      <view 
        class="absolute bottom-10 left-0 right-0 flex justify-center z-20 transition-opacity duration-300"
        :style="{ opacity: headerOpacity }"
      >
        <view class="px-4 py-1 bg-black/50 backdrop-blur rounded-full">
          <text class="text-white text-sm">{{ currentIndex + 1 }} / {{ images.length }}</text>
        </view>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref, computed, watch } from 'vue';

const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },
  images: {
    type: Array,
    default: () => []
  },
  initialIndex: {
    type: Number,
    default: 0
  }
});

const emit = defineEmits(['close']);

const currentIndex = ref(0);
const isClosing = ref(false);
const isAnimating = ref(false);

// Drag state
const startY = ref(0);
const startX = ref(0);
const currentY = ref(0);
const isDragging = ref(false);
const isRestoring = ref(false);
const headerOpacity = ref(1);

const maskStyle = computed(() => {
  const baseOpacity = 0.9;
  let opacity = baseOpacity;
  if (isDragging.value || isRestoring.value) {
    // Fade out background as we drag
    const dragProgress = Math.min(1, Math.abs(currentY.value) / 400);
    opacity = baseOpacity * (1 - dragProgress);
  }
  const transition = isRestoring.value ? 'transition: background-color 0.3s ease-out;' : 'transition: none;';
  return `background-color: rgba(0, 0, 0, ${opacity}); ${transition}`;
});

const imageStyle = computed(() => {
  if (!isDragging.value && !isRestoring.value) return '';
  const scale = Math.max(0.5, 1 - Math.abs(currentY.value) / 1000);
  const transition = isRestoring.value ? 'transition: transform 0.3s cubic-bezier(0.2, 0.8, 0.2, 1);' : 'transition: none;';
  return `transform: translate3d(0, ${currentY.value}px, 0) scale(${scale}); ${transition}`;
});

watch(() => props.show, (val) => {
  if (val) {
    currentIndex.value = props.initialIndex;
    isClosing.value = false;
    isAnimating.value = true;
    resetDrag();
    // Remove animation class after animation ends to prevent transform issues with swiper
    setTimeout(() => {
      isAnimating.value = false;
    }, 600);
  }
});

watch(() => props.initialIndex, (val) => {
  currentIndex.value = val;
});

const resetDrag = () => {
  startY.value = 0;
  startX.value = 0;
  currentY.value = 0;
  isDragging.value = false;
  isRestoring.value = false;
  headerOpacity.value = 1;
};

const handleTouchStart = (e) => {
  if (isAnimating.value || isRestoring.value || e.touches.length > 1) return;
  startY.value = e.touches[0].clientY;
  startX.value = e.touches[0].clientX;
  isDragging.value = false;
};

const handleTouchMove = (e) => {
  if (isAnimating.value || isRestoring.value || e.touches.length > 1) return;
  const touchY = e.touches[0].clientY;
  const touchX = e.touches[0].clientX;
  const deltaY = touchY - startY.value;
  const deltaX = touchX - startX.value;
  
  // Only start dragging if moved vertically significantly AND more than horizontally
  if (!isDragging.value) {
    if (Math.abs(deltaY) > 10 && Math.abs(deltaY) > Math.abs(deltaX)) {
      isDragging.value = true;
    }
  }
  
  if (isDragging.value) {
    currentY.value = deltaY;
    // Fade out UI elements
    headerOpacity.value = Math.max(0, 1 - Math.abs(deltaY) / 200);
  }
};

const handleTouchEnd = () => {
  if (!isDragging.value) return;
  
  if (Math.abs(currentY.value) > 150) {
    // Dragged far enough to close
    handleClose();
  } else {
    // Reset position with animation
    isRestoring.value = true;
    isDragging.value = false;
    currentY.value = 0;
    headerOpacity.value = 1;
    
    setTimeout(() => {
      isRestoring.value = false;
    }, 300);
  }
};

const onSwiperChange = (e) => {
  currentIndex.value = e.detail.current;
};

const handleClose = () => {
  isClosing.value = true;
  // Wait for animation to finish
  setTimeout(() => {
    emit('close');
    isClosing.value = false;
  }, 500);
};

const handleSave = () => {
  const currentImage = props.images[currentIndex.value];
  
  // #ifdef H5
  // Create a temporary link to download
  const link = document.createElement('a');
  link.href = currentImage;
  link.download = `image-${Date.now()}`;
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
  uni.showToast({
    title: 'Downloading...',
    icon: 'none'
  });
  // #endif

  // #ifndef H5
  uni.downloadFile({
    url: currentImage,
    success: (res) => {
      if (res.statusCode === 200) {
        uni.saveImageToPhotosAlbum({
          filePath: res.tempFilePath,
          success: () => {
            uni.showToast({
              title: 'Saved to Album',
              icon: 'success'
            });
          },
          fail: () => {
            uni.showToast({
              title: 'Save Failed',
              icon: 'none'
            });
          }
        });
      }
    }
  });
  // #endif
};
</script>

<style scoped>
.perspective-1000 {
  perspective: 1000px;
}

@keyframes flipIn {
  0% {
    opacity: 0;
    transform: rotateY(-90deg) scale(0.8);
  }
  100% {
    opacity: 1;
    transform: rotateY(0) scale(1);
  }
}

@keyframes flipOut {
  0% {
    opacity: 1;
    transform: rotateY(0) scale(1);
  }
  100% {
    opacity: 0;
    transform: rotateY(90deg) scale(0.8);
  }
}

.animate-flip-in {
  animation: flipIn 0.6s cubic-bezier(0.23, 1, 0.32, 1) forwards;
  transform-origin: center center;
}

.animate-flip-out {
  animation: flipOut 0.5s cubic-bezier(0.23, 1, 0.32, 1) forwards;
  transform-origin: center center;
}

@keyframes fadeInDown {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in-down {
  animation: fadeInDown 0.6s ease-out 0.3s backwards;
}
</style>