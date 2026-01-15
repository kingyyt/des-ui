<template>
  <view class="px-6 py-4 w-full pb-24">
    <view class="flex justify-between items-center mb-4">
      <text class="text-white text-lg font-bold opacity-90">My Groups</text>
      <text class="text-gray-400 text-xs">View All</text>
    </view>
    
    <view class="grid grid-cols-2 gap-4">
      <view 
        v-for="(group, index) in groups" 
        :key="index"
        class="bg-[#1e293b]/50 backdrop-blur-md border border-white/5 rounded-2xl p-4 flex flex-col items-center text-center space-y-3"
      >
        <u-image :src="group.image" width="48px" height="48px" shape="circle" class="border-2 border-white/10"></u-image>
        <view>
          <text class="text-white text-sm font-bold block">{{ group.name }}</text>
          <text class="text-gray-400 text-xs block mt-1">{{ group.members }} members</text>
        </view>
        <view class="w-full h-8 bg-blue-600/20 rounded-lg flex items-center justify-center text-blue-400 text-xs font-bold mt-2">
          Join
        </view>
      </view>
    </view>
  </view>
</template>

<script setup>
const groups = [
  { name: 'UI Designers', members: '12k', image: '/static/img/pexels-alohaphotostudio-5319953.jpg' },
  { name: 'UX Research', members: '8.5k', image: '/static/img/pexels-bayram-yalcin-86843184-19240480.jpg' },
  { name: 'Prototyping', members: '5k', image: '/static/img/pexels-ekamelev-920163.jpg' },
  { name: 'Frontend Dev', members: '15k', image: '/static/img/pexels-francesco-ungaro-2440427.jpg' }
];
</script>