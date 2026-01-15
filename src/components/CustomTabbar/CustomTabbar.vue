<template>
  <view class="fixed bottom-0 left-0 right-0 p-4 pb-8 z-50">
    <!-- Tabbar Container -->
    <view class="bg-[#0f172a] rounded-full h-16 flex items-center justify-around px-6 shadow-2xl border border-white/10 relative">
      
      <!-- Search Tab -->
      <view class="flex items-center justify-center w-12 h-12" @click="switchTab('/pages/search/search')">
        <u-icon name="search" :color="current === 0 ? '#ffffff' : '#94a3b8'" size="28"></u-icon>
      </view>

      <!-- Center Floating Button (Collections) -->
      <view class="absolute -top-6 left-1/2 -translate-x-1/2">
        <view class="w-16 h-16 bg-[#3b82f6] rounded-full flex items-center justify-center shadow-lg shadow-blue-500/50" @click="switchTab('/pages/index/index')">
          <u-icon name="folder" color="#ffffff" size="32"></u-icon>
        </view>
      </view>

      <!-- Placeholder for center space -->
      <view class="w-12"></view>

      <!-- Profile Tab -->
      <view class="flex items-center justify-center w-12 h-12" @click="switchTab('/pages/profile/profile')">
        <u-icon name="account" :color="current === 2 ? '#ffffff' : '#94a3b8'" size="28"></u-icon>
      </view>
      
    </view>
  </view>
</template>

<script setup>
const props = defineProps({
  current: {
    type: Number,
    default: 0
  }
});

const switchTab = (path) => {
  // 修复切换 Tab 报错问题，确保路径不带参数且格式正确
  uni.switchTab({
    url: path,
    fail: (err) => {
      console.error('Switch tab failed:', err);
    }
  });
};
</script>