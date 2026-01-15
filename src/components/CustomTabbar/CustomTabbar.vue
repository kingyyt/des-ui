<template>
  <view class="fixed bottom-0 left-0 right-0 p-4 pb-8 z-50">
    <!-- Tabbar Container -->
    <view class="bg-[#0f172a]/90 backdrop-blur-xl rounded-full h-16 flex items-center justify-around px-6 shadow-[0_8px_32px_rgba(0,0,0,0.4)] border border-white/10 relative">
      
      <!-- Personal Tab -->
      <view 
        class="group flex items-center justify-center w-12 h-12 relative" 
        @click="switchTab('/pages/personal/personal')"
      >
        <!-- Active Glow -->
        <view 
          class="absolute inset-0 bg-blue-400/20 rounded-full blur-md transition-all duration-500"
          :class="current === 0 ? 'opacity-100 scale-100' : 'opacity-0 scale-50'"
        ></view>
        
        <u-icon 
          name="email-fill" 
          :color="current === 0 ? '#ffffff' : '#94a3b8'" 
          size="28"
          class="transition-all duration-500 relative z-10"
          :class="current === 0 ? 'animate-pop' : 'group-active:scale-90'"
        ></u-icon>
      </view>

      <!-- Center Floating Button (Collections) -->
      <view class="absolute -top-6 left-1/2 -translate-x-1/2">
        <view 
          class="w-16 h-16 bg-gradient-to-tr from-[#3b82f6] to-[#60a5fa] rounded-full flex items-center justify-center shadow-lg shadow-blue-500/50 transition-all duration-300" 
          :class="current === 1 ? 'scale-110 ring-4 ring-blue-500/30' : 'active:scale-95'"
          @click="switchTab('/pages/index/index')"
        >
          <u-icon 
            name="home-fill" 
            color="#ffffff" 
            size="32"
            class="transition-all duration-500"
            :class="current === 1 ? 'animate-rotate-in' : ''"
          ></u-icon>
        </view>
      </view>

      <!-- Placeholder for center space -->
      <view class="w-12"></view>

      <!-- Profile Tab -->
      <view 
        class="group flex items-center justify-center w-12 h-12 relative" 
        @click="switchTab('/pages/profile/profile')"
      >
        <!-- Active Glow -->
        <view 
          class="absolute inset-0 bg-blue-400/20 rounded-full blur-md transition-all duration-500"
          :class="current === 2 ? 'opacity-100 scale-100' : 'opacity-0 scale-50'"
        ></view>

        <u-icon 
          name="account-fill" 
          :color="current === 2 ? '#ffffff' : '#94a3b8'" 
          size="28"
          class="transition-all duration-500 relative z-10"
          :class="current === 2 ? 'animate-pop' : 'group-active:scale-90'"
        ></u-icon>
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

<style scoped>
@keyframes pop {
  0% { transform: scale(0.8); }
  50% { transform: scale(1.2); }
  100% { transform: scale(1); }
}
.animate-pop {
  animation: pop 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}

@keyframes rotateIn {
  from { transform: rotate(-180deg) scale(0.5); opacity: 0; }
  to { transform: rotate(0) scale(1); opacity: 1; }
}
.animate-rotate-in {
  animation: rotateIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}
</style>