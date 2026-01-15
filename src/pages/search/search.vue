<template>
  <view class="min-h-screen bg-[#0a1128] flex flex-col items-center justify-center text-white">
    <text class="text-2xl">Search Page</text>
    <CustomTabbar :current="0" />
  </view>
</template>

<script setup>
import CustomTabbar from '@/components/CustomTabbar/CustomTabbar.vue';
</script>