<template>
  <view class="min-h-screen bg-[#0a1128] text-white pb-24 overflow-hidden font-sans">
    <!-- Header -->
    <view class="pt-12 px-6 flex justify-between items-center animate-fade-in-down">
      <text class="text-xl font-bold tracking-wide">Collections</text>
      <view class="flex items-center space-x-4">
        <view class="relative">
          <u-icon name="bell" color="#ffffff" size="24"></u-icon>
          <view class="absolute -top-1 -right-1 w-2 h-2 bg-red-500 rounded-full"></view>
        </view>
        <u-image src="/static/img/pexels-didsss-1643919.jpg" width="32px" height="32px" shape="circle" border="1px solid rgba(255,255,255,0.2)"></u-image>
      </view>
    </view>

    <!-- Greeting -->
    <view class="mt-8 px-6 animate-slide-in-left" style="animation-delay: 0.2s; opacity: 0; animation-fill-mode: forwards;">
      <text class="text-4xl font-bold leading-tight block">Hello,</text>
      <text class="text-4xl font-bold leading-tight block">Emily Kane</text>
    </view>

    <!-- Card List -->
    <view v-for="(card, index) in cards" :key="card.id" 
          class="mt-8 mx-6 bg-[#1e293b] rounded-[32rpx] p-5 shadow-2xl animate-slide-in-right transform transition-all duration-500 hover:scale-[1.02]"
          :style="{ animationDelay: `${0.4 + index * 0.2}s`, opacity: 0, animationFillMode: 'forwards' }">
      
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

      <!-- Image Grid (1 Left Big + 4 Right Small) -->
      <view class="grid grid-cols-2 gap-3 h-64">
        <!-- Big Image (Left) -->
        <view class="col-span-1 h-full rounded-2xl overflow-hidden">
          <u-image :src="card.images[0]" width="100%" height="100%" mode="aspectFill" @click="openPreview(index, 0)"></u-image>
        </view>
        
        <!-- Small Images Grid (Right) -->
        <view class="col-span-1 grid grid-cols-2 grid-rows-2 gap-2 h-full">
          <view class="rounded-xl overflow-hidden">
            <u-image :src="card.images[1]" width="100%" height="100%" mode="aspectFill" @click="openPreview(index, 1)"></u-image>
          </view>
          <view class="rounded-xl overflow-hidden">
            <u-image :src="card.images[2]" width="100%" height="100%" mode="aspectFill" @click="openPreview(index, 2)"></u-image>
          </view>
          <view class="rounded-xl overflow-hidden">
            <u-image :src="card.images[3]" width="100%" height="100%" mode="aspectFill" @click="openPreview(index, 3)"></u-image>
          </view>
          <view class="rounded-xl overflow-hidden">
            <u-image :src="card.images[4]" width="100%" height="100%" mode="aspectFill" @click="openPreview(index, 4)"></u-image>
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
          @click="openDetail(card)"
        >
          <u-icon name="arrow-right" color="#ffffff" size="20"></u-icon>
        </view>
      </view>
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

<script>
import CustomTabbar from '@/components/CustomTabbar/CustomTabbar.vue';
import ImagePreview from '@/components/ImagePreview/ImagePreview.vue';
import PropertyDetail from '@/components/PropertyDetail/PropertyDetail.vue';

export default {
  components: {
    CustomTabbar,
    ImagePreview,
    PropertyDetail
  },
  data() {
    return {
      title: 'Hello',
      // Detail state
      detailShow: false,
      currentCard: {},
      
      // Preview state
      previewShow: false,
      previewImages: [],
      previewIndex: 0,
      
      cards: [
        {
          id: 1,
          user: {
            name: 'Kurisu Makise',
            handle: '@steinsgate',
            avatar: '/static/img/pexels-didsss-1643919.jpg'
          },
          images: [
            '/static/img/pexels-ekamelev-920157.jpg',
            '/static/img/pexels-ekamelev-920163.jpg',
            '/static/img/pexels-francesco-ungaro-2440427.jpg',
            '/static/img/pexels-alohaphotostudio-5319953.jpg',
            '/static/img/pexels-bayram-yalcin-86843184-19240480.jpg'
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
            avatar: '/static/img/pexels-alexander-mass-748453803-18868016.jpg'
          },
          images: [
            '/static/img/pexels-francesco-ungaro-3630025.jpg',
            '/static/img/pexels-leonardo-lamas-32247393-7001550.jpg',
            '/static/img/pexels-tomtookit-1914663-3538659.jpg',
            '/static/img/pexels-didsss-1643919.jpg',
            '/static/img/pexels-ekamelev-920157.jpg'
          ],
          stats: {
            likes: 104,
            comments: 42
          }
        }
      ]
    }
  },
  onLoad() {
    // Hide native tabbar just in case App.vue didn't catch it
    uni.hideTabBar({
      fail: () => {}
    })
  },
  methods: {
    openPreview(cardIndex, imageIndex) {
      const allImages = this.cards.reduce((acc, card) => acc.concat(card.images || []), []);
      const offset = this.cards
        .slice(0, cardIndex)
        .reduce((sum, card) => sum + ((card.images && card.images.length) || 0), 0);

      this.previewImages = allImages;
      this.previewIndex = offset + imageIndex;
      this.previewShow = true;
    },
    openDetail(card) {
      this.currentCard = card;
      this.detailShow = true;
    }
  }
}
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
