<template>
  <view class="flex justify-around items-center px-8 py-6 w-full max-w-sm mx-auto">
    <view 
      v-for="(badge, index) in badges" 
      :key="index"
      class="flex flex-col items-center animate-scale-in"
      :style="{ animationDelay: `${index * 100}ms` }"
    >
      <view class="w-16 h-16 rounded-full bg-gradient-to-br from-white/10 to-white/5 backdrop-blur-md border border-white/10 flex items-center justify-center mb-2 shadow-lg relative group">
        <view class="absolute inset-0 bg-white/20 rounded-full filter blur-md opacity-0 group-hover:opacity-100 transition-opacity duration-300"></view>
        <text class="text-3xl relative z-10">{{ badge.icon }}</text>
      </view>
    </view>
  </view>
</template>

<script setup>
const badges = [
  { icon: '🥇', label: 'Rank 1' },
  { icon: '🛡️', label: 'Guardian' },
  { icon: '👑', label: 'Premium' }
];
</script>

<style scoped>
@keyframes scaleIn {
  from {
    opacity: 0;
    transform: scale(0.5);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.animate-scale-in {
  animation: scaleIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
  opacity: 0;
}
</style>