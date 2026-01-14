<template>
  <view v-if="visible" class="fixed inset-0 z-50 bg-black">
    <!-- Background Swiper (Fixed) -->
    <view class="fixed top-0 left-0 w-full h-[55vh] z-0 animate-slide-down" @touchmove.stop.prevent>
      <swiper class="w-full h-full" :indicator-dots="true" indicator-active-color="#ffffff" circular autoplay :interval="4000" :duration="500">
        <swiper-item v-for="(img, index) in card.images" :key="index">
          <image :src="img" mode="aspectFill" class="w-full h-full"></image>
          <!-- Gradient overlay for better text contrast/visuals -->
          <view class="absolute inset-0 bg-gradient-to-b from-black/30 via-transparent to-transparent"></view>
        </swiper-item>
      </swiper>
    </view>

    <!-- Top Navigation (Fixed) -->
    <view class="fixed top-0 left-0 w-full z-20 pt-12 px-6 flex justify-between items-center pointer-events-none animate-slide-down" @touchmove.stop.prevent>
        <view class="w-10 h-10 bg-black/20 backdrop-blur-md rounded-full flex items-center justify-center pointer-events-auto active:scale-95 transition-transform" @click="handleClose">
            <u-icon name="arrow-left" color="#ffffff" size="20"></u-icon>
        </view>
        <view class="w-10 h-10 bg-black/20 backdrop-blur-md rounded-full flex items-center justify-center pointer-events-auto active:scale-95 transition-transform">
            <u-icon name="bookmark" color="#ffffff" size="20"></u-icon>
        </view>
    </view>

    <!-- Scrollable Content Overlay -->
    <scroll-view 
      scroll-y 
      class="relative w-full h-full z-10"
      :show-scrollbar="false"
      :enhanced="true"
      :bounces="false"
      @touchmove.stop
    >
      <!-- Spacer to reveal the image behind -->
      <view class="w-full h-[45vh]"></view>
      
      <!-- White Content Sheet -->
      <view class="bg-white rounded-t-[40rpx] min-h-[60vh] pb-10 animate-slide-up relative shadow-[0_-10px_40px_rgba(0,0,0,0.1)]">
        <!-- Drag Handle Indicator -->
        <view class="w-full flex justify-center pt-3 pb-1">
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
    </scroll-view>
  </view>
</template>

<script>
export default {
  name: 'PropertyDetail',
  props: {
    show: {
      type: Boolean,
      default: false
    },
    card: {
      type: Object,
      default: () => ({})
    }
  },
  data() {
    return {
      visible: false
    }
  },
  watch: {
    show(val) {
      if (val) {
        this.visible = true;
      } else {
        setTimeout(() => {
            this.visible = false;
        }, 300); // Wait for animation out if implemented, currently instant close
      }
    }
  },
  methods: {
    handleClose() {
      this.$emit('close');
    }
  }
}
</script>

<style scoped>
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

@keyframes slideUp {
  from {
    transform: translateY(100%);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.animate-slide-up {
  animation: slideUp 0.5s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

.animate-slide-down {
  animation: slideDown 0.5s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

.animate-fade-in {
  animation: fadeIn 0.5s ease-out forwards;
}
</style>