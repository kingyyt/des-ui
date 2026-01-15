<template>
  <view class="min-h-screen bg-[#0a1128] text-white flex flex-col relative overflow-hidden font-sans">
    
    <!-- Header Placeholder -->
    <view class="h-12"></view>

    <!-- Main Content Area -->
    <view class="flex-1 flex flex-col items-center justify-center relative z-10 pb-32">
      
      <!-- Floating Dot Animation -->
      <view class="absolute top-[10%] animate-float opacity-80">
        <view class="w-3 h-3 bg-purple-400 rounded-full blur-[1px] shadow-[0_0_15px_rgba(192,132,252,0.8)]"></view>
      </view>

      <!-- Spiral Line Container -->
      <view class="w-[300px] h-[400px] relative mb-8 flex items-center justify-center">
         <canvas canvas-id="spiralCanvas" id="spiralCanvas" class="w-full h-full"></canvas>
      </view>

      <!-- Text Content -->
      <view class="text-center px-10 animate-fade-in-up z-20">
        <text class="text-5xl font-light tracking-wide block mb-6 text-white/90">Avoue!</text>
        <text class="text-gray-400 text-sm font-light leading-relaxed block max-w-[280px] mx-auto">
          A way to tip people even if you are tight on cash at the moment
        </text>
      </view>

      <!-- Button -->
      <view class="mt-16 animate-fade-in-up z-20" style="animation-delay: 0.2s;">
        <button 
          class="bg-[#d4b978] text-[#0a1128] rounded-full px-12 py-3 font-bold text-lg border-none shadow-lg active:scale-95 transition-transform flex items-center justify-center"
          @click="handleGetStarted"
        >
          Get Started
          <u-icon name="arrow-right" color="#0a1128" size="16" class="ml-2"></u-icon>
        </button>
      </view>

    </view>

    <!-- Overlay Component -->
    <PersonalOverlay 
      :show="showOverlay" 
      :animated="isAnimated"
      @close="handleCloseOverlay"
    />

    <!-- Custom Tabbar (only visible when overlay is hidden) -->
    <CustomTabbar :current="0" class="transition-opacity duration-500" :class="{ 'opacity-0 pointer-events-none': showOverlay }" />
  </view>
</template>

<script setup>
import { onMounted, ref, getCurrentInstance } from 'vue';
import CustomTabbar from '@/components/CustomTabbar/CustomTabbar.vue';
import PersonalOverlay from './components/PersonalOverlay.vue';

const showOverlay = ref(false);
const isAnimated = ref(false);
let ctx = null;
let points = [];

const handleGetStarted = () => {
  isAnimated.value = true;
  // Start spiral vanish animation immediately
  animateSpiralVanish();
  
  // Delay overlay appearance by 0.5 second
  setTimeout(() => {
    showOverlay.value = true;
  }, 300);
};

const handleCloseOverlay = () => {
  showOverlay.value = false;
  // Restore spiral
  drawSpiral(0);
  // Reset animation state after transition
  setTimeout(() => {
    isAnimated.value = false;
  }, 1000);
};

// Canvas Logic
const initSpiralPoints = () => {
  const width = 300;
  const startY = 50;
  const endY = 350;
  const loops = 5.5;
  const centerX = width / 2;
  const newPoints = [];
  const steps = 150; 
  
  for (let i = 0; i <= steps; i++) {
    const t = i / steps; 
    const y = startY + t * (endY - startY);
    const baseRadius = 15 + t * 80; 
    const angle = t * loops * 2 * Math.PI - (Math.PI / 2); 
    const irregularRadius = baseRadius + Math.sin(t * 10) * 5;
    const x = centerX + Math.sin(angle) * irregularRadius;
    newPoints.push({x, y});
  }
  return newPoints;
};

const drawSpiral = (startIndex) => {
  if (!ctx || points.length === 0) return;
  
  ctx.setStrokeStyle('#ffffff');
  ctx.setLineWidth(1.5);
  ctx.setLineCap('round');
  ctx.setLineJoin('round');
  ctx.setShadow(0, 0, 8, 'rgba(255, 255, 255, 0.5)');
  
  const visiblePoints = points.slice(Math.floor(startIndex));
  
  ctx.beginPath();
  if (visiblePoints.length > 0) {
    ctx.moveTo(visiblePoints[0].x, visiblePoints[0].y);
    
    for (let i = 1; i < visiblePoints.length - 1; i++) {
      const p0 = visiblePoints[i];
      const p1 = visiblePoints[i + 1];
      const midX = (p0.x + p1.x) / 2;
      const midY = (p0.y + p1.y) / 2;
      ctx.quadraticCurveTo(p0.x, p0.y, midX, midY);
    }
    
    if (visiblePoints.length > 1) {
       const last = visiblePoints[visiblePoints.length - 1];
       ctx.lineTo(last.x, last.y);
    }
  }
  
  ctx.stroke();
  ctx.draw();
};

const easeOutCubic = (x) => {
  return 1 - Math.pow(1 - x, 3);
};

const animateSpiralVanish = () => {
  const duration = 800; 
  const startTime = Date.now();
  const totalPoints = points.length;
  
  const animate = () => {
    const elapsed = Date.now() - startTime;
    let progress = elapsed / duration;
    
    if (progress > 1) progress = 1;
    
    // Use easeOut to match CSS transition
    const easedProgress = easeOutCubic(progress);
    
    const startIndex = totalPoints * easedProgress;
    drawSpiral(startIndex);
    
    if (progress < 1) {
      setTimeout(animate, 16);
    }
  };
  
  animate();
};

onMounted(() => {
  ctx = uni.createCanvasContext('spiralCanvas', getCurrentInstance());
  points = initSpiralPoints();
  drawSpiral(0);
});
</script>

<style>
/* Animations */
@keyframes float {
  0%, 100% { transform: translateY(0); opacity: 0.8; }
  50% { transform: translateY(-15px); opacity: 1; }
}

.animate-float {
  animation: float 4s ease-in-out infinite;
}

@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(30px); }
  to { opacity: 1; transform: translateY(0); }
}

.animate-fade-in-up {
  animation: fadeInUp 1s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}
</style>