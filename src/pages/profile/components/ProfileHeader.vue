<template>
  <view class="relative w-full z-10">
    <!-- Background with Frosted Glass and Curve -->
    <view class="absolute top-0 left-0 w-full h-[320px] overflow-hidden z-0">
      <!-- Gradient Background -->
      <view class="absolute inset-0 bg-gradient-to-br from-blue-600/30 via-purple-600/30 to-pink-600/30"></view>
      <!-- Frosted Glass -->
      <view class="absolute inset-0 backdrop-blur-3xl"></view>
      
      <!-- Curve Shape Mask (CSS Clip Path) -->
      <!-- We use a wrapper to apply the clip path to the background container -->
    </view>
    
    <!-- We need the shape to cut the background. 
         Better approach: Put the content in a container, and put the background behind it with the shape. 
    -->
    <view 
      class="w-full h-[320px] absolute top-0 left-0 z-0 bg-gradient-to-br from-blue-900/80 via-purple-900/80 to-[#0a1128]/50 backdrop-blur-2xl"
      style="border-bottom-left-radius: 30% 10%; border-bottom-right-radius: 30% 20%; clip-path: ellipse(150% 100% at 50% 0%);"
    ></view>

    <!-- Content -->
    <view class="relative z-10 pt-16 px-6 pb-8 flex flex-col items-center text-center">
      <!-- Avatar -->
      <view class="relative mb-4 group">
        <view class="absolute -inset-1 bg-gradient-to-r from-pink-500 via-purple-500 to-blue-500 rounded-full opacity-75 blur transition duration-500 group-hover:opacity-100"></view>
        <u-image 
          src="/static/img/1.png" 
          width="100px" 
          height="100px" 
          shape="circle" 
          class="relative border-4 border-[#0a1128]"
        ></u-image>
        <view class="absolute bottom-1 right-1 w-6 h-6 bg-green-500 border-2 border-[#0a1128] rounded-full"></view>
      </view>

      <!-- Name & Role -->
      <view class="animate-fade-in-up">
        <text class="text-3xl font-bold text-white block mb-1">Sandy</text>
        <view class="flex items-center justify-center space-x-2 text-gray-300">
          <text class="text-sm">Designer at AppleMax</text>
          <u-icon name="map-fill" color="#f87171" size="14"></u-icon>
        </view>
      </view>
    </view>
  </view>
</template>

<script setup>
</script>

<style scoped>
.animate-fade-in-up {
  animation: fadeInUp 0.8s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>