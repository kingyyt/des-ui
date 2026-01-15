<template>
  <view class="px-6 py-4 w-full">
    <text class="text-white text-lg font-bold mb-4 block opacity-90">Sandy Highlights</text>
    
    <scroll-view scroll-x class="w-full whitespace-nowrap" :show-scrollbar="false">
      <view class="flex space-x-4 pb-2">
        <view 
          v-for="(item, index) in highlights" 
          :key="index"
          class="inline-block w-40 h-28 rounded-2xl overflow-hidden relative flex-shrink-0 border border-white/10 shadow-lg"
        >
          <u-image :src="item.image" width="100%" height="100%" mode="aspectFill"></u-image>
          <!-- Play Icon Overlay -->
          <view class="absolute inset-0 bg-black/20 flex items-center justify-center backdrop-blur-[2px]">
            <view class="w-8 h-8 rounded-full bg-white/20 backdrop-blur-md flex items-center justify-center border border-white/30">
              <u-icon name="play-right-fill" color="#ffffff" size="12"></u-icon>
            </view>
          </view>
        </view>
      </view>
    </scroll-view>
  </view>
</template>

<script setup>
const highlights = [
  { image: '/static/img/pexels-francesco-ungaro-3630025.jpg' },
  { image: '/static/img/pexels-leonardo-lamas-32247393-7001550.jpg' },
  { image: '/static/img/pexels-tomtookit-1914663-3538659.jpg' },
  { image: '/static/img/pexels-ekamelev-920157.jpg' }
];
</script>