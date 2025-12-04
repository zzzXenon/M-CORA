<script setup lang="ts">
   import { ref, computed, onMounted, onUnmounted } from 'vue';

   interface Props {
      data: number[];
   }

   const props = defineProps<Props>();

   const chartContainer = ref<HTMLElement | null>(null);
   const width = ref(0);
   const height = ref(0);
   const hoverIdx = ref(-1);

   const updateDims = () => {
   if (chartContainer.value) {
      width.value = chartContainer.value.clientWidth;
      height.value = chartContainer.value.clientHeight;
   }
   };

   onMounted(() => {
      window.addEventListener('resize', updateDims);
      setTimeout(updateDims, 100);
   });

   onUnmounted(() => window.removeEventListener('resize', updateDims));

   const points = computed(() => {
      if (!width.value || !height.value || !props.data.length) return [];
      const max = Math.max(...props.data) * 1.1; 
      const min = Math.min(...props.data) * 0.9;
      const range = max - min;
      
      return props.data.map((val, i) => ({
         x: (i / (props.data.length - 1)) * width.value,
         y: height.value - ((val - min) / range) * height.value
      }));
   });

   const activePoint = computed(() => {
      if (hoverIdx.value === -1) return null;
      return points.value[hoverIdx.value] || null;
   });

   const linePath = computed(() => {
      if (!points.value.length) return '';
      return `M ${points.value.map(p => `${p.x},${p.y}`).join(' L ')}`;
   });

   const areaPath = computed(() => {
      if (!points.value.length) return '';
      return `${linePath.value} L ${width.value},${height.value} L 0,${height.value} Z`;
   });

   const handleMouseMove = (e: MouseEvent) => {
      if (!width.value || !chartContainer.value) return;
      const rect = chartContainer.value.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const idx = Math.round((x / width.value) * (props.data.length - 1));
      hoverIdx.value = Math.max(0, Math.min(idx, props.data.length - 1));
   };

   const hideTooltip = () => hoverIdx.value = -1;
</script>

<template>
  <div class="w-full h-full" ref="chartContainer" @mousemove="handleMouseMove" @mouseleave="hideTooltip">
    <svg class="w-full h-full overflow-visible">
      <defs>
        <linearGradient id="chartGradient" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#10b981" stop-opacity="0.2"/>
          <stop offset="100%" stop-color="#10b981" stop-opacity="0"/>
        </linearGradient>
      </defs>
      
      <!-- Grid Lines -->
      <line v-for="i in 5" :key="i" x1="0" :y1="i * (height/5)" :x2="width" :y2="i * (height/5)" stroke="#f1f5f9" stroke-width="1"/>

      <!-- Paths -->
      <path :d="areaPath" fill="url(#chartGradient)" />
      <path :d="linePath" fill="none" stroke="#10b981" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" />

      <!-- Hover Point -->
      <circle v-if="activePoint" 
        :cx="activePoint.x" 
        :cy="activePoint.y" 
        r="6" fill="#fff" stroke="#10b981" stroke-width="3" />
    </svg>

    <!-- Tooltip -->
    <div v-if="activePoint" 
         class="pointer-events-none absolute -translate-x-1/2 -translate-y-full z-10 bg-slate-800 text-white text-xs rounded px-2 py-1 shadow-lg"
         :style="{ left: activePoint.x + 'px', top: (activePoint.y - 10) + 'px' }">
        Day {{ hoverIdx + 1 }}: <strong>{{ data[hoverIdx] }} T</strong>
    </div>
  </div>
</template>