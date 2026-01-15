<template>
  <view class="min-h-screen bg-[#0a1128] text-white pb-24 overflow-hidden font-sans relative">
    <!-- Animated Background -->
    <BackgroundCanvas />

    <!-- Header -->
    <view 
      class="px-6 flex justify-start items-center animate-fade-in-down relative z-10"
      :style="{ paddingTop: headerPaddingTop, height: '88px', alignItems: 'center' }"
    >
      <view class="flex items-center space-x-4 mr-4">
        <view class="relative">
          <u-icon name="bell" color="#ffffff" size="24"></u-icon>
          <view class="absolute -top-1 -right-1 w-2 h-2 bg-red-500 rounded-full"></view>
        </view>
        <u-image src="/static/img/1.png" width="32px" height="32px" shape="circle" border="1px solid rgba(255,255,255,0.2)"></u-image>
      </view>
      <text class="text-xl font-bold tracking-wide">Collections</text>
    </view>

    <!-- Greeting -->
    <view class="mt-8 px-6 animate-slide-in-left relative z-10" style="animation-delay: 0.2s; opacity: 0; animation-fill-mode: forwards;">
      <text class="text-4xl font-bold leading-tight block">Hello,</text>
      <text class="text-4xl font-bold leading-tight block">Emily Kane</text>
    </view>

    <!-- Card List -->
    <view class="px-6 pb-20">
      <FeedCard 
        v-for="(card, index) in cards" 
        :key="card.id"
        :card="card"
        class="mt-8 animate-slide-in-right"
        :style="{ animationDelay: `${0.4 + index * 0.2}s`, opacity: 0, animationFillMode: 'forwards' }"
        @preview="handlePreview"
        @detail="openDetail"
      />
    </view>

    <!-- Custom Tabbar -->
    <CustomTabbar :current="1" />
    
    <!-- Image Preview Component -->
    <ImagePreview 
      :show="previewShow" 
      :images="previewImages" 
      :initial-index="previewIndex"
      @close="previewShow = false"
    />

    <!-- Property Detail Component -->
    <PropertyDetail
      :show="detailShow"
      :card="currentCard"
      @close="detailShow = false"
    />
  </view>
</template>

<script setup>
import { ref } from 'vue';
import { onLoad } from '@dcloudio/uni-app';
import CustomTabbar from '@/components/CustomTabbar/CustomTabbar.vue';
import ImagePreview from '@/components/ImagePreview/ImagePreview.vue';
import PropertyDetail from '@/components/PropertyDetail/PropertyDetail.vue';
import BackgroundCanvas from './components/BackgroundCanvas.vue';
import FeedCard from './components/FeedCard.vue';

const title = ref('Hello');
// Detail state
const detailShow = ref(false);
const currentCard = ref({});

// Preview state
const previewShow = ref(false);
const previewImages = ref([]);
const previewIndex = ref(0);
const headerPaddingTop = ref('48px');

const cards = ref([
  {
    id: 1,
    user: {
      name: 'Kurisu Makise',
      handle: '@steinsgate',
      avatar: '/static/img/1.png'
    },
    title: 'Laboratory Memories',
    description: 'A collection of moments from the Future Gadget Laboratory. These are precious memories that span across different world lines.',
    images: [
      '/static/img/2.png',
      '/static/img/3.png',
      '/static/img/4.png',
      '/static/img/5.png',
      '/static/img/7.png'
    ],
    stats: {
      likes: 24,
      comments: 12
    }
  },
  {
    id: 2,
    user: {
      name: 'Okabe Rintarou',
      handle: '@madscientist',
      avatar: '/static/img/8.png'
    },
    title: 'Operation Skuld',
    description: 'The final mission to reach Steins Gate. El Psy Kongroo.',
    images: [
      '/static/img/1.png',
      '/static/img/2.png',
      '/static/img/3.png',
      '/static/img/4.png'
    ],
    stats: {
      likes: 104,
      comments: 42
    }
  },
  {
    id: 3,
    user: {
      name: 'Mayuri Shiina',
      handle: '@tuturu',
      avatar: '/static/img/3.png'
    },
    title: 'Stardust Shakehand',
    description: 'Looking at the stars with Okarin.',
    images: [
      '/static/img/2.png',
      '/static/img/3.png',
      '/static/img/4.png'
    ],
    stats: {
      likes: 89,
      comments: 33
    }
  },
  {
    id: 4,
    user: {
      name: 'Daru',
      handle: '@superhacker',
      avatar: '/static/img/1.png'
    },
    title: 'Server Maintenance',
    description: 'Upgrading the IBN 5100 setup.',
    images: [
      '/static/img/5.png',
      '/static/img/7.png'
    ],
    stats: {
      likes: 56,
      comments: 8
    }
  },
  {
    id: 5,
    user: {
      name: 'Suzuha Amane',
      handle: '@parttimewarrior',
      avatar: '/static/img/3.png'
    },
    title: 'Time Travel',
    description: 'Just arrived from 2036.',
    images: [
      '/static/img/2.png'
    ],
    stats: {
      likes: 120,
      comments: 15
    }
  }
]);

onLoad(() => {
  // Hide native tabbar just in case App.vue didn't catch it
  uni.hideTabBar({
    fail: () => {}
  });
  
  // Calculate safe area for header
  const systemInfo = uni.getSystemInfoSync();
  let top = systemInfo.statusBarHeight || 0;
  
  // #ifdef MP-WEIXIN
  const menuButton = uni.getMenuButtonBoundingClientRect();
  if (menuButton) {
      // Use menuButton.top + 8px to ensure good clearance and alignment
      top = menuButton.top + 4;
  }
  // #endif
  
  headerPaddingTop.value = `${top}px`;
});

const handlePreview = ({ card, index }) => {
  // Show only images from the current card
  const currentCardImages = card.images || [];
  previewImages.value = currentCardImages;
  previewIndex.value = index;
  previewShow.value = true;
};

const openDetail = (card) => {
  currentCard.value = card;
  detailShow.value = true;
};
</script>

<style>
@keyframes slideInLeft {
  from {
    opacity: 0;
    transform: translateX(-50px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes slideInRight {
  from {
    opacity: 0;
    transform: translateX(50px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
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

.animate-slide-in-left {
  animation: slideInLeft 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}

.animate-slide-in-right {
  animation: slideInRight 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}

.animate-fade-in-down {
  animation: fadeInDown 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}

/* Image mode fix for web/h5 */
.mode-aspect-fill {
  object-fit: cover;
}
</style>