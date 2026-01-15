<template>
  <view class="min-h-screen bg-[#0a1128] text-white pb-24 relative overflow-hidden font-sans">
    <!-- Animated Background -->
    <BackgroundCanvas />

    <!-- Header Section -->
    <ProfileHeader />

    <!-- Badges -->
    <view class="mt-[-60px] relative z-20">
      <ProfileBadges />
    </view>

    <!-- Highlights -->
    <ProfileHighlights />

    <!-- About -->
    <ProfileAbout />

    <!-- Groups -->
    <ProfileGroups />

    <!-- Custom Tabbar -->
    <CustomTabbar :current="2" />
  </view>
</template>

<script setup>
import CustomTabbar from '@/components/CustomTabbar/CustomTabbar.vue';
import BackgroundCanvas from '@/pages/index/components/BackgroundCanvas.vue';
import ProfileHeader from './components/ProfileHeader.vue';
import ProfileBadges from './components/ProfileBadges.vue';
import ProfileHighlights from './components/ProfileHighlights.vue';
import ProfileAbout from './components/ProfileAbout.vue';
import ProfileGroups from './components/ProfileGroups.vue';
</script>

<style>
/* Global styles for this page if needed */
</style>