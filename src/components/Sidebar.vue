<script setup lang="ts">
    import { ref } from 'vue';
    import { 
    PlusCircle, Server, MoreVertical, 
    Edit2, Trash2, LogOut, LogIn, Settings 
    } from 'lucide-vue-next';

    interface Machine {
    id: string;
    name: string;
    location: string;
    }

    defineProps<{
    machines: Machine[];
    selectedMachineId: string | null;
    isCollapsed: boolean;
    isMobile: boolean;
    isSidebarOpen: boolean;
    isLoggedIn: boolean;
    }>();

    const emit = defineEmits<{
    (e: 'toggleSidebar'): void;
    (e: 'toggleCollapse'): void;
    (e: 'selectMachine', id: string): void;
    (e: 'openModal'): void;
    (e: 'openProfile'): void;
    (e: 'login'): void;
    (e: 'logout'): void;
    }>();

    const expandedMachineId = ref<string | null>(null);

    const toggleMachineOptions = (id: string) => {
    expandedMachineId.value = expandedMachineId.value === id ? null : id;
    };
</script>

<template>
  <aside :class="[
      'fixed lg:static inset-y-0 left-0 z-30 bg-slate-900 text-slate-300 flex flex-col transition-all duration-300 ease-in-out border-r border-slate-800 shadow-xl',
      isCollapsed ? 'w-20' : 'w-72',
      isMobile && !isSidebarOpen ? '-translate-x-full' : 'translate-x-0'
  ]">
      <!-- Logo Area (sidebar toggle) -->
      <div @click="!isMobile ? emit('toggleCollapse') : null" 
           class="h-16 flex items-center px-4 border-b border-slate-800 shrink-0 cursor-pointer hover:bg-slate-800 transition-colors group select-none" 
           :class="isCollapsed ? 'justify-center' : 'justify-between'"
           title="Toggle Sidebar">
          
          <!-- Expanded -->
          <div v-if="!isCollapsed" class="flex items-center gap-2 font-bold text-white text-xl tracking-tight">
              <div class="w-8 h-8 bg-emerald-500 rounded-lg flex items-center justify-center text-slate-900 group-hover:bg-emerald-400 transition-colors">M</div>
              <span>M-CORA</span>
          </div>

          <!-- Collapsed -->
          <div v-else class="w-8 h-8 bg-emerald-500 rounded-lg flex items-center justify-center text-slate-900 font-bold group-hover:bg-emerald-400 transition-colors">M</div>
      </div>

      <!-- Navigation -->
      <div class="flex-1 overflow-y-auto custom-scroll py-4 flex flex-col gap-2 px-3">
          <button @click="emit('openModal')" 
                  class="flex items-center gap-3 p-3 rounded-lg bg-emerald-600/10 text-emerald-400 hover:bg-emerald-600/20 transition-all group cursor-pointer">
              <PlusCircle class="w-5 h-5 shrink-0" />
              <span v-if="!isCollapsed" class="font-medium whitespace-nowrap">Add Machine</span>
          </button>

          <div v-if="!isCollapsed" class="text-xs font-semibold text-slate-500 mt-4 px-3 uppercase tracking-wider">Machines</div>
          <div v-else class="h-4"></div>

          <div v-for="machine in machines" :key="machine.id" class="relative group">
              <div @click="emit('selectMachine', machine.id)" 
                   :class="[
                      'flex items-center gap-3 p-3 rounded-lg cursor-pointer transition-all border border-transparent',
                      selectedMachineId === machine.id ? 'bg-indigo-600 text-white shadow-lg shadow-indigo-900/20' : 'hover:bg-slate-800'
                   ]">
                  <Server :class="['w-5 h-5 shrink-0', selectedMachineId === machine.id ? 'text-white' : 'text-slate-500']" />
                  
                  <div v-if="!isCollapsed" class="flex-1 min-w-0">
                      <div class="font-medium truncate">{{ machine.name }}</div>
                      <div class="text-xs opacity-70 truncate">{{ machine.id }}</div>
                  </div>

                  <button v-if="!isCollapsed" 
                          @click.stop="toggleMachineOptions(machine.id)"
                          class="p-1 hover:bg-white/10 rounded opacity-0 group-hover:opacity-100 transition-opacity cursor-pointer">
                      <MoreVertical class="w-4 h-4" />
                  </button>
              </div>

              <!-- Options Dropdown -->
              <div v-if="expandedMachineId === machine.id && !isCollapsed" 
                   class="mt-1 ml-4 pl-4 border-l border-slate-700 space-y-1 animate-fadeIn">
                  <button class="flex items-center gap-2 text-sm text-slate-400 hover:text-white p-1.5 w-full text-left rounded hover:bg-slate-800 cursor-pointer">
                      <Edit2 class="w-3 h-3" /> Rename
                  </button>
                  <button class="flex items-center gap-2 text-sm text-red-400 hover:text-red-300 p-1.5 w-full text-left rounded hover:bg-slate-800/50 cursor-pointer">
                      <Trash2 class="w-3 h-3" /> Delete
                  </button>
              </div>
          </div>
      </div>

      <!-- Auth / User Profile -->
      <div class="p-4 border-t border-slate-800 bg-slate-900/50">
          <div v-if="isLoggedIn">
              <div @click="emit('openProfile')" 
                   :class="['flex items-center gap-3 cursor-pointer hover:bg-slate-800 p-2 -m-2 rounded-lg transition-colors', isCollapsed ? 'justify-center' : '']">
                  <div class="w-9 h-9 rounded-full bg-gradient-to-tr from-indigo-500 to-purple-500 flex items-center justify-center text-white font-bold text-sm shadow-md shrink-0">
                      JD
                  </div>
                  <div v-if="!isCollapsed" class="min-w-0 flex-1">
                      <div class="text-sm font-medium text-white truncate">John Doe</div>
                      <div class="text-xs text-slate-500 truncate">Operator</div>
                  </div>
                  <button v-if="!isCollapsed" @click.stop="emit('logout')" class="p-2 text-slate-400 hover:text-white hover:bg-slate-700 rounded transition-colors cursor-pointer" title="Logout">
                      <LogOut class="w-4 h-4" />
                  </button>
              </div>
          </div>
          <div v-else>
              <button @click="emit('login')" class="w-full flex items-center justify-center gap-2 bg-slate-800 hover:bg-slate-700 text-white py-2 rounded-lg transition-colors text-sm font-medium">
                  <LogIn class="w-4 h-4" />
                  <span v-if="!isCollapsed">Login</span>
              </button>
          </div>
      </div>
  </aside>
</template>