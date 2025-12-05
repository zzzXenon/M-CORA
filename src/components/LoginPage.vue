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
      if (username.value === 'admin' && password.value === 'admin') {
         emit('login-success');
      } else {
         errorMsg.value = 'Invalid credentials. Please try again.';
         isLoading.value = false;
      }
   }, 800);
   };
</script>

<template>
   <div class="min-h-screen w-full flex bg-white">
      <!-- LEFT SIDE -->
      <div class="hidden lg:block w-1/2 relative bg-slate-900 overflow-hidden">
         <!-- IMAGE PLACEHOLDER -->
         <img 
         src="/src/assets/login-splitgate.png" 
         alt="Login Background Image" 
         class="absolute inset-0 w-full h-full object-cover opacity-80"
         />
         <!-- GRADIENT OVERLAY -->
         <div class="absolute inset-0 bg-gradient-to-t from-emerald-900/90 via-slate-900/40 to-transparent"></div>
         
         <div class="absolute bottom-0 left-0 p-12 text-white z-10">
         <h2 class="text-4xl font-bold mb-4 tracking-tight">Sustainable Future</h2>
         <p class="text-emerald-100 max-w-md text-lg leading-relaxed">
            AI green energy save the planet type shii.
         </p>
         </div>
      </div>

      <!-- RIGHT SIDE -->
      <div class="w-full lg:w-1/2 flex items-center justify-center p-8">
         <div class="w-full max-w-md space-y-8 animate-fadeIn">

         <div class="text-center">
            <div class="w-16 h-16 bg-gradient-to-tr from-emerald-500 to-teal-500 rounded-2xl flex items-center justify-center mx-auto mb-6 shadow-xl shadow-emerald-500/20">
               <span class="text-3xl font-bold text-white">M</span>
            </div>
            <h1 class="text-2xl font-bold text-slate-900">Welcome Back</h1>
            <p class="text-slate-500 mt-2">Sign in to access the M-CORA dashboard</p>
         </div>

         <!-- LOGIN FORM -->
         <form @submit.prevent="handleLogin" class="space-y-6 mt-8">
            <div class="space-y-2">
               <label class="text-sm font-medium text-slate-700" for="username">Username</label>
               <div class="relative">
               <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                  <User class="h-5 w-5 text-slate-400" />
               </div>
               <input 
                  id="username"
                  v-model="username" 
                  type="text" 
                  class="block w-full pl-10 pr-3 py-3 border border-slate-200 rounded-xl text-slate-900 placeholder-slate-400 focus:outline-none focus:ring-2 focus:ring-emerald-500/20 focus:border-emerald-500 transition-all bg-slate-50 focus:bg-white"
                  placeholder="Enter your username"
                  required
               />
               </div>
            </div>

            <div class="space-y-2">
               <label class="text-sm font-medium text-slate-700" for="password">Password</label>
               <div class="relative">
                  <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                     <Lock class="h-5 w-5 text-slate-400" />
                  </div>
                  <input 
                     id="password"
                     v-model="password" 
                     :type="showPassword ? 'text' : 'password'" 
                     class="block w-full pl-10 pr-10 py-3 border border-slate-200 rounded-xl text-slate-900 placeholder-slate-400 focus:outline-none focus:ring-2 focus:ring-emerald-500/20 focus:border-emerald-500 transition-all bg-slate-50 focus:bg-white"
                     placeholder="••••••••"
                     required
                  />
                  <button 
                     type="button" 
                     @click="showPassword = !showPassword"
                     class="absolute inset-y-0 right-0 pr-3 flex items-center text-slate-400 hover:text-emerald-600 transition-colors"
                  >
                     <component :is="showPassword ? EyeOff : Eye" class="h-5 w-5" />
                  </button>
               </div>
            </div>

            <div v-if="errorMsg" class="p-3 bg-red-50 text-red-600 text-sm rounded-lg flex items-center gap-2 border border-red-100">
               <div class="w-1.5 h-1.5 rounded-full bg-red-500"></div> {{ errorMsg }}
            </div>

            <button 
               type="submit" 
               :disabled="isLoading"
               class="w-full flex items-center justify-center py-3.5 px-4 border border-transparent rounded-xl shadow-lg shadow-emerald-600/20 text-sm font-bold text-white bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-700 hover:to-teal-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-emerald-500 transition-all transform hover:scale-[1.01] active:scale-[0.99] disabled:opacity-70 disabled:cursor-not-allowed"
            >
               <span v-if="isLoading" class="animate-pulse">Signing in...</span>
               <span v-else class="flex items-center gap-2">Sign In <ArrowRight class="w-4 h-4" /></span>
            </button>
         </form>

        <p class="text-center text-xs text-slate-400 mt-6">
          &copy; 2025. Made by TirtaSustaina. All rights reserved.
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
   @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
   }
   .animate-fadeIn {
      animation: fadeIn 0.6s ease-out forwards;
   }
</style>