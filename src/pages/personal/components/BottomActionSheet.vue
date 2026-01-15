<template>
  <view 
    class="absolute inset-x-0 bottom-0 z-50 transition-all duration-700 cubic-bezier(0.25, 1, 0.5, 1) overflow-hidden shadow-[0_-8px_30px_rgba(0,0,0,0.3)]"
    :class="expanded ? 'h-[85vh] bg-[#0f172a] rounded-t-[40px]' : 'h-24 bg-[#1e293b] rounded-t-[50%]'"
    @touchstart="handleTouchStart"
    @touchmove="handleTouchMove"
    @touchend="handleTouchEnd"
  >
    <!-- Handle Bar (Always visible) -->
    <view 
      class="w-12 h-1 bg-white/20 rounded-full mx-auto mt-4 transition-opacity duration-300"
    ></view>

    <!-- Item 1: Grid -> Tip Waiter -->
    <view 
      class="absolute transition-all duration-700 cubic-bezier(0.25, 1, 0.5, 1) overflow-hidden shadow-lg"
      :class="expanded && isGroup1Active ? 'rounded-[32px]' : 'rounded-[16px]'"
      :style="item1Style"
      @click.stop="handleItemClick(1)"
    >
       <view class="w-full h-full bg-[#dbeafe] flex flex-col items-center justify-center relative">
          <u-icon name="grid" color="#1e3a8a" :size="expanded && isGroup1Active ? 0 : 22" class="transition-all duration-300" :class="expanded && isGroup1Active ? 'opacity-0' : 'opacity-100'"></u-icon>
          
          <!-- Expanded Content -->
          <view class="absolute inset-0 flex flex-col items-center justify-center transition-opacity duration-500 delay-200" :class="expanded && isGroup1Active ? 'opacity-100' : 'opacity-0 pointer-events-none'">
             <text class="text-3xl font-bold text-[#1e3a8a] mb-2">Tip your waiter</text>
             <text class="text-gray-500 text-center max-w-[200px]">There's a QR code in your check. Scan it to tip a waiter</text>
             <button class="mt-8 bg-black text-white rounded-full px-10 py-3 font-bold">Scan QR-code</button>
          </view>
       </view>
    </view>

    <!-- Item 2: Scan -> Map -->
    <view 
      class="absolute transition-all duration-700 cubic-bezier(0.25, 1, 0.5, 1) overflow-hidden shadow-lg"
      :class="expanded && isGroup1Active ? 'rounded-[32px]' : 'rounded-[16px]'"
      :style="item2Style"
      @click.stop="handleItemClick(2)"
    >
       <view class="w-full h-full bg-[#fef9c3] flex flex-col items-center justify-center relative">
          <u-icon name="scan" color="#854d0e" :size="expanded && isGroup1Active ? 0 : 22" class="transition-all duration-300" :class="expanded && isGroup1Active ? 'opacity-0' : 'opacity-100'"></u-icon>
          
          <!-- Expanded Content (Map Placeholder) -->
          <view class="absolute inset-0 transition-opacity duration-500 delay-200" :class="expanded && isGroup1Active ? 'opacity-100' : 'opacity-0 pointer-events-none'">
             <u-image src="/static/img/pexels-ekamelev-920157.jpg" width="100%" height="100%" mode="aspectFill"></u-image>
             <!-- Avatar overlays -->
             <view class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 w-16 h-16 rounded-full border-4 border-white overflow-hidden shadow-lg">
                <u-image src="/static/img/pexels-didsss-1643919.jpg" width="100%" height="100%"></u-image>
             </view>
          </view>
       </view>
    </view>

    <!-- Item 3: File -> Documents -->
    <view 
      class="absolute transition-all duration-700 cubic-bezier(0.25, 1, 0.5, 1) overflow-hidden shadow-sm"
      :class="expanded && isGroup2Active ? 'rounded-[32px]' : 'rounded-[16px]'"
      :style="item3Style"
      @click.stop="handleItemClick(3)"
    >
       <view class="w-full h-full bg-[#f3e8ff] flex flex-col items-center justify-center relative">
          <u-icon name="file-text" color="#6b21a8" :size="expanded && isGroup2Active ? 0 : 22" class="transition-all duration-300" :class="expanded && isGroup2Active ? 'opacity-0' : 'opacity-100'"></u-icon>
          
          <!-- Expanded Content -->
          <view class="absolute inset-0 flex flex-col items-center justify-center transition-opacity duration-500 delay-200" :class="expanded && isGroup2Active ? 'opacity-100' : 'opacity-0 pointer-events-none'">
             <text class="text-3xl font-bold text-[#6b21a8] mb-2">Documents</text>
             <text class="text-gray-500 text-center max-w-[200px]">Access your recent files and contracts here.</text>
             <button class="mt-8 bg-black text-white rounded-full px-10 py-3 font-bold">View Files</button>
          </view>
       </view>
    </view>

    <!-- Item 4: List -> History -->
    <view 
      class="absolute transition-all duration-700 cubic-bezier(0.25, 1, 0.5, 1) overflow-hidden shadow-sm"
      :class="expanded && isGroup2Active ? 'rounded-[32px]' : 'rounded-[16px]'"
      :style="item4Style"
      @click.stop="handleItemClick(4)"
    >
       <view class="w-full h-full bg-[#d1fae5] flex flex-col items-center justify-center relative">
          <u-icon name="list" color="#065f46" :size="expanded && isGroup2Active ? 0 : 22" class="transition-all duration-300" :class="expanded && isGroup2Active ? 'opacity-0' : 'opacity-100'"></u-icon>
          
          <!-- Expanded Content -->
          <view class="absolute inset-0 flex flex-col items-center justify-center transition-opacity duration-500 delay-200" :class="expanded && isGroup2Active ? 'opacity-100' : 'opacity-0 pointer-events-none'">
             <text class="text-3xl font-bold text-[#065f46] mb-2">History</text>
             <text class="text-gray-500 text-center max-w-[200px]">Check your recent transactions and activity.</text>
             <button class="mt-8 bg-black text-white rounded-full px-10 py-3 font-bold">View History</button>
          </view>
       </view>
    </view>

  </view>
</template>

<script setup>
import { ref, computed, watch } from 'vue';

const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false
  }
});

const emit = defineEmits(['update:modelValue']);

// Internal ref for uncontrolled mode
const internalExpanded = ref(false);
const activeIndex = ref(1);

const expanded = computed({
  get: () => props.modelValue,
  set: (val) => {
    internalExpanded.value = val;
    emit('update:modelValue', val);
  }
});

// Reset active index when collapsed
watch(expanded, (newVal) => {
  if (!newVal) {
    // Optional: reset to 1 after transition?
    // setTimeout(() => activeIndex.value = 1, 300);
  }
});

let startY = 0;

const toggleExpand = () => {
  expanded.value = !expanded.value;
};

const handleItemClick = (index) => {
  if (!expanded.value) {
    expanded.value = true;
    activeIndex.value = index;
  } else {
    activeIndex.value = index;
  }
};

const handleTouchStart = (e) => {
  startY = e.touches[0].clientY;
};

const handleTouchMove = (e) => {
  const currentY = e.touches[0].clientY;
  const deltaY = currentY - startY;
  // Pull up to expand
  if (deltaY < -30 && !expanded.value) {
    expanded.value = true;
  } 
  // Pull down to collapse
  else if (deltaY > 30 && expanded.value) {
    expanded.value = false;
  }
};
const handleTouchEnd = () => {};

// Helper to determine if an item is in Card mode
const isGroup1Active = computed(() => activeIndex.value <= 2); // 1 or 2
const isGroup2Active = computed(() => activeIndex.value >= 3); // 3 or 4

// Dynamic Styles for transitions
const item1Style = computed(() => {
  if (!expanded.value) {
    return { bottom: '20px', left: 'calc(50% - 132px)', width: '48px', height: '48px', zIndex: 10 };
  }

  // Expanded State
  if (isGroup1Active.value) {
    // Card Mode
    if (activeIndex.value === 1) {
      // Main Card
      return { bottom: '40%', left: '24px', width: 'calc(100% - 48px)', height: '45%', zIndex: 30 };
    } else {
      // Peek Top (moved up)
      return { bottom: '20%', left: '24px', width: 'calc(100% - 48px)', height: '45%', zIndex: 10 };
    }
  } else {
    // Button Mode (Left Slot)
    return { bottom: '32px', left: '24px', width: 'calc(50% - 32px)', height: '48px', zIndex: 20 };
  }
});

const item2Style = computed(() => {
  if (!expanded.value) {
    return { bottom: '20px', left: 'calc(50% - 60px)', width: '48px', height: '48px', zIndex: 10 };
  }

  // Expanded State
  if (isGroup1Active.value) {
    // Card Mode
    if (activeIndex.value === 1) {
      // Secondary Card (Bottom)
      return { bottom: '120px', left: '24px', width: 'calc(100% - 48px)', height: '45%', zIndex: 20 };
    } else {
      // Main Card
      return { bottom: '40%', left: '24px', width: 'calc(100% - 48px)', height: '45%', zIndex: 30 };
    }
  } else {
    // Button Mode (Right Slot)
    return { bottom: '32px', right: '24px', width: 'calc(50% - 32px)', height: '48px', zIndex: 20 };
  }
});

const item3Style = computed(() => {
  if (!expanded.value) {
    return { bottom: '20px', left: 'calc(50% + 12px)', width: '48px', height: '48px', zIndex: 10 };
  }

  // Expanded State
  if (isGroup2Active.value) {
    // Card Mode
    if (activeIndex.value === 3) {
      // Main Card
      return { bottom: '40%', left: '24px', width: 'calc(100% - 48px)', height: '45%', zIndex: 30 };
    } else {
      // Peek Top
      return { bottom: '20%', left: '24px', width: 'calc(100% - 48px)', height: '45%', zIndex: 10 };
    }
  } else {
    // Button Mode (Left Slot)
    return { bottom: '32px', left: '24px', width: 'calc(50% - 32px)', height: '48px', zIndex: 20 };
  }
});

const item4Style = computed(() => {
  if (!expanded.value) {
    return { bottom: '20px', left: 'calc(50% + 84px)', width: '48px', height: '48px', zIndex: 10 };
  }

  // Expanded State
  if (isGroup2Active.value) {
    // Card Mode
    if (activeIndex.value === 3) {
      // Secondary Card (Bottom)
      return { bottom: '120px', left: '24px', width: 'calc(100% - 48px)', height: '45%', zIndex: 20 };
    } else {
      // Main Card
      return { bottom: '40%', left: '24px', width: 'calc(100% - 48px)', height: '45%', zIndex: 30 };
    }
  } else {
    // Button Mode (Right Slot)
    return { bottom: '32px', right: '24px', width: 'calc(50% - 32px)', height: '48px', zIndex: 20 };
  }
});
</script>