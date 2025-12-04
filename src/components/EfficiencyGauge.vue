<script setup lang="ts">
   import { computed } from 'vue';
   import { ArrowUp, ArrowDown} from 'lucide-vue-next';

   interface Props {
      score: number;
      trend: 'up' | 'down';
   }

   const props = defineProps<Props>();

   const circumference = 2 * Math.PI * 56;
   const dashOffset = computed(() => circumference - (props.score / 100) * circumference);

   const statusColor = computed(() => {
      if (props.score >= 90) return 'bg-emerald-50 text-emerald-700 border-emerald-100';
      if (props.score >= 70) return 'bg-amber-50 text-amber-700 border-amber-100';
      return 'bg-red-50 text-red-700 border-red-100';
   });

   const scoreColor = computed(() => {
      if (props.score >= 90) return 'text-emerald-500';
      if (props.score >= 70) return 'text-amber-500';
      return 'text-red-500';
   });

   const statusText = computed(() => {
      if (props.score >= 90) return 'OPTIMAL';
      if (props.score >= 70) return 'DEGRADING';
      return 'CRITICAL';
   });
</script>

<template>
  <div class="bg-white rounded-2xl p-6 shadow-sm border border-slate-100 relative overflow-hidden flex flex-col justify-between">
    <div class="flex justify-between items-start z-10">
      <div>
        <h3 class="font-semibold text-slate-700">Energy Efficiency by AI</h3>
        <p class="text-xs text-slate-500 mt-1">Operational Score</p>
      </div>
      <span :class="['px-2.5 py-1 rounded-full text-xs font-bold border', statusColor]">
        {{ statusText }}
      </span>
    </div>

    <div class="flex items-center justify-center my-4 relative">
      <svg class="w-32 h-32 transform -rotate-90">
        <circle cx="64" cy="64" r="56" stroke="currentColor" stroke-width="12" fill="transparent" class="text-slate-100" />
        <circle 
          cx="64" cy="64" r="56" 
          stroke="currentColor" stroke-width="12" fill="transparent" 
          :class="scoreColor"
          stroke-linecap="round"
          :style="{ strokeDasharray: circumference, strokeDashoffset: dashOffset, transition: 'stroke-dashoffset 1s ease-in-out' }" 
        />
      </svg>
      <div class="absolute flex flex-col items-center">
        <span class="text-3xl font-bold text-slate-800">{{ score.toFixed(0) }}%</span>
        <span class="text-xs font-medium flex items-center gap-1" :class="trend === 'up' ? 'text-emerald-500' : 'text-amber-500'">
          <component :is="trend === 'up' ? ArrowUp : ArrowDown" class="w-3 h-3" />
          {{ trend === 'up' ? 'Improving' : 'Declining' }}
        </span>
      </div>
    </div>
  </div>
</template>