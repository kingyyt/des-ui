<template>
  <view class="fixed inset-0 pointer-events-none z-0 overflow-hidden">
    <canvas 
      canvas-id="bgCanvas" 
      id="bgCanvas" 
      class="w-full h-full opacity-30"
    ></canvas>
  </view>
</template>

<script setup>
import { onMounted, onUnmounted, getCurrentInstance } from 'vue';

let ctx = null;
let animationId = null;
const agents = [];

class Agent {
  constructor(width, height) {
    this.width = width;
    this.height = height;
    this.reset();
  }

  reset() {
    // Start at a random position
    const x = Math.random() * this.width;
    const y = Math.random() * this.height;
    
    // Path points (corners)
    this.path = [{x, y}, {x, y}]; // Start with 2 points to avoid empty path issues
    
    // Pick a random cardinal direction
    const dirs = [{x: 1, y: 0}, {x: -1, y: 0}, {x: 0, y: 1}, {x: 0, y: -1}];
    this.dir = dirs[Math.floor(Math.random() * dirs.length)];
    
    this.speed = 1 + Math.random() * 0.5; // Random speed
    this.maxLength = 300 + Math.random() * 200; // Random length
    this.color = Math.random() > 0.5 ? '#3b82f6' : '#8b5cf6'; // Blue or Purple
    this.widthLine = 2;
    
    // Timer for next turn
    this.turnCounter = 0;
    this.nextTurn = 50 + Math.random() * 100;
  }

  update() {
    // 1. Move the head (last point)
    const head = this.path[this.path.length - 1];
    head.x += this.dir.x * this.speed;
    head.y += this.dir.y * this.speed;

    // 2. Turn logic
    this.turnCounter++;
    if (this.turnCounter > this.nextTurn) {
      // Check bounds to force turn back
      const margin = 50;
      let turned = false;
      
      // Determine possible new directions (90 deg turns)
      let newDirs = [];
      if (this.dir.x !== 0) { // Moving horizontally
        newDirs = [{x: 0, y: 1}, {x: 0, y: -1}];
      } else { // Moving vertically
        newDirs = [{x: 1, y: 0}, {x: -1, y: 0}];
      }
      
      // Simple bias to keep on screen
      if (head.x < margin) newDirs = newDirs.filter(d => d.x >= 0);
      if (head.x > this.width - margin) newDirs = newDirs.filter(d => d.x <= 0);
      if (head.y < margin) newDirs = newDirs.filter(d => d.y >= 0);
      if (head.y > this.height - margin) newDirs = newDirs.filter(d => d.y <= 0);
      
      if (newDirs.length > 0) {
        this.dir = newDirs[Math.floor(Math.random() * newDirs.length)];
        // Add new point for the turn
        this.path.push({x: head.x, y: head.y});
        this.turnCounter = 0;
        this.nextTurn = 50 + Math.random() * 100;
        turned = true;
      } else {
        // Stuck or OOB, just turn randomly
         if (this.dir.x !== 0) this.dir = {x: 0, y: 1};
         else this.dir = {x: 1, y: 0};
         this.path.push({x: head.x, y: head.y});
         this.turnCounter = 0;
      }
    }
    
    // Bounds check - reset if way off screen
    if (head.x < -100 || head.x > this.width + 100 || head.y < -100 || head.y > this.height + 100) {
      this.reset();
      return;
    }

    // 3. Trim tail
    this.trimTail();
  }

  trimTail() {
    let totalLen = 0;
    // Calculate total length
    for (let i = 0; i < this.path.length - 1; i++) {
      const p1 = this.path[i];
      const p2 = this.path[i+1];
      totalLen += Math.hypot(p2.x - p1.x, p2.y - p1.y);
    }

    if (totalLen > this.maxLength) {
      const excess = totalLen - this.maxLength;
      const p0 = this.path[0];
      const p1 = this.path[1];
      const segLen = Math.hypot(p1.x - p0.x, p1.y - p0.y);

      if (excess >= segLen) {
        // Remove first point completely
        this.path.shift();
        // Recurse/Iterate if still too long (simple approach: just run next frame)
      } else {
        // Move p0 towards p1
        const ratio = excess / segLen;
        // p0.x += (p1.x - p0.x) * ratio; // This jumps, we need to move exactly excess amount
        // Vector p0->p1
        const dx = p1.x - p0.x;
        const dy = p1.y - p0.y;
        // Normalize
        const len = Math.hypot(dx, dy);
        if (len > 0) {
           p0.x += (dx / len) * excess;
           p0.y += (dy / len) * excess;
        }
      }
    }
  }

  draw(ctx) {
    if (this.path.length < 2) return;

    ctx.beginPath();
    ctx.setStrokeStyle(this.color);
    ctx.setLineWidth(this.widthLine);
    ctx.setLineCap('round');
    ctx.setLineJoin('round');

    // Draw with rounded corners using arcTo
    // Start at p0
    ctx.moveTo(this.path[0].x, this.path[0].y);

    // Iterate through intermediate points (corners)
    for (let i = 1; i < this.path.length - 1; i++) {
      const pCurrent = this.path[i];
      const pNext = this.path[i+1];
      // Use arcTo for rounded corner
      // We need a radius.
      const radius = 20;
      ctx.arcTo(pCurrent.x, pCurrent.y, pNext.x, pNext.y, radius);
    }

    // Line to the last point (Head)
    const last = this.path[this.path.length - 1];
    ctx.lineTo(last.x, last.y);

    ctx.stroke();
    
    // Optional: Draw a glowing head
    ctx.beginPath();
    ctx.arc(last.x, last.y, 4, 0, Math.PI * 2);
    ctx.setFillStyle(this.color);
    ctx.fill();
  }
}

onMounted(() => {
  const instance = getCurrentInstance();
  ctx = uni.createCanvasContext('bgCanvas', instance);
  
  const sys = uni.getSystemInfoSync();
  const w = sys.windowWidth;
  const h = sys.windowHeight;

  // Create 2 agents
  agents.push(new Agent(w, h));
  agents.push(new Agent(w, h));

  const loop = () => {
    // Clear canvas? 
    // In uni-app canvas, drawing automatically clears unless reserved? 
    // Wait, ctx.draw() overwrites by default.
    // But we need to redraw everything fresh.
    
    agents.forEach(agent => agent.update());
    
    // Draw all
    agents.forEach(agent => agent.draw(ctx));
    
    ctx.draw(); // false = clear canvas before drawing

    // Request next frame
    // uni-app doesn't have requestAnimationFrame globally in logic layer sometimes, 
    // but in script setup it should work on H5. For MP, might need fallback.
    // We'll use setTimeout fallback if needed, but standard requestAnimationFrame usually works.
    animationId = setTimeout(loop, 1000 / 60); 
    // Note: using setTimeout for better cross-platform compatibility in simple cases if raf is tricky in some uni-app contexts
  };

  loop();
});

onUnmounted(() => {
  if (animationId) clearTimeout(animationId);
});
</script>

<style scoped>
</style>