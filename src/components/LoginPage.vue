<script setup lang="ts">
   import { ref } from 'vue';
   import { Eye, EyeOff, Lock, User, ArrowRight } from 'lucide-vue-next';

   const emit = defineEmits<{
      (e: 'login-success'): void;
   }>();

   const username = ref('');
   const password = ref('');
   const showPassword = ref(false);
   const errorMsg = ref('');
   const isLoading = ref(false);

   const handleLogin = () => {
   errorMsg.value = '';
   isLoading.value = true;

   // Simulate network delay for better UX
   setTimeout(() => {
      emit('login-success');
   }, 800);
   };
</script>

<template>
  <div class="min-h-screen w-full flex bg-white">
    
    <!-- LEFT SIDE -->
    <div class="hidden lg:block w-1/2 relative overflow-hidden bg-slate-900">
      <img 
        src="/src/assets/login-splitgate-2.jpg"
        alt="Welcome Background"
        class="absolute inset-0 w-full h-full object-cover scale-105 opacity-90"
      />

      <!-- Dark overlay -->
      <div class="absolute inset-0 bg-gradient-to-t from-[#57BEED]/45 via-slate-900/60 to-transparent"></div>

      <!-- Text -->
      <div class="absolute inset-0 flex flex-col items-center justify-center text-center p-14 text-white z-10">
         <h2 class="text-4xl font-extrabold mb-3 tracking-tight drop-shadow-lg">
         Towards a Sustainable Future
         </h2>
         <p class="text-white max-w-md text-lg leading-relaxed opacity-90">
         Empowering a cleaner world through AI-driven green energy innovation.
         </p>

      </div>
    </div>

    <!-- RIGHT SIDE -->
    <div class="w-full lg:w-1/2 flex items-center justify-center p-10">
      <div class="w-full max-w-md space-y-10 animate-fadeIn">

        <!-- LOGO -->
        <div class="text-center">
          <div class="w-16 h-16 bg-gradient-to-br from-[#57BEED] to-teal-500 rounded-2xl 
                      flex items-center justify-center mx-auto mb-6 shadow-lg shadow-[#57BEED]">
            <span class="text-3xl font-bold text-white">M</span>
          </div>
          <h1 class="text-3xl font-semibold text-slate-900">Welcome Back</h1>
          <p class="text-slate-500 mt-2 tracking-wide">
            Sign in to access the M-CORA dashboard
          </p>
        </div>

        <!-- FORM -->
        <form @submit.prevent="handleLogin" class="space-y-6 bg-white/60 backdrop-blur-xl p-8 rounded-2xl border border-slate-200 shadow-xl">
          
          <!-- USERNAME -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-slate-700">Username</label>
            <div class="relative">
              <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                <User class="h-5 w-5 text-slate-400" />
              </div>
              <input
                v-model="username"
                type="text"
                placeholder="Enter your username"
                required
                class="block w-full pl-10 pr-3 py-3 border border-slate-300 rounded-xl bg-slate-50 
                       text-slate-900 placeholder-slate-400 
                       focus:ring-2 focus:ring-[#57BEED] focus:border-[#57BEED]
                       transition-all"
              />
            </div>
          </div>

          <!-- PASSWORD -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-slate-700">Password</label>
            <div class="relative">
              <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                <Lock class="h-5 w-5 text-slate-400" />
              </div>
              <input
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                placeholder="••••••••"
                required
                class="block w-full pl-10 pr-10 py-3 border border-slate-300 rounded-xl bg-slate-50 
                       text-slate-900 placeholder-slate-400
                       focus:ring-2 focus:ring-[#57BEED] focus:border-[#57BEED]
                       transition-all"
              />
              <button
                type="button"
                @click="showPassword = !showPassword"
                class="absolute inset-y-0 right-0 pr-3 flex items-center 
                       text-slate-400 hover:text-[#57BEED] transition"
              >
                <component :is="showPassword ? EyeOff : Eye" class="h-5 w-5" />
              </button>
            </div>
          </div>

          <!-- ERROR MESSAGE -->
          <div 
            v-if="errorMsg" 
            class="p-3 bg-red-50 border border-red-200 text-red-600 text-sm rounded-lg flex items-center gap-2"
          >
            <div class="w-2 h-2 rounded-full bg-red-500"></div>
            {{ errorMsg }}
          </div>

          <!-- BUTTON -->
          <button
            type="submit"
            :disabled="isLoading"
            class="w-full flex items-center justify-center py-3.5 rounded-xl font-semibold text-white 
                   bg-gradient-to-r from-[#57BEED] to-teal-600
                   hover:from-[#57BEED] hover:to-teal-700
                   shadow-lg shadow-[#57BEED]
                   transition transform hover:scale-[1.02] active:scale-[0.98]
                   disabled:opacity-60 disabled:cursor-not-allowed"
          >
            <span v-if="isLoading" class="animate-pulse">Signing in...</span>
            <span v-else class="flex items-center gap-2">
              Sign In <ArrowRight class="w-4 h-4" />
            </span>
          </button>

        </form>

        <p class="text-center text-xs text-slate-400">
          &copy; 2025 — Made by TirtaSustaina
        </p>

      </div>
    </div>

  </div>
</template>

<style scoped>
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(14px); }
  to   { opacity: 1; transform: translateY(0); }
}

.animate-fadeIn {
  animation: fadeIn 0.7s ease-out forwards;
}
</style>
