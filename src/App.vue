<script setup lang="ts">
    import { ref, reactive, computed, onMounted } from 'vue';
    import { Menu, Leaf, TrendingUp, ShieldCheck, AlertCircle, Box, Sprout, Droplet, MapPin, X, User } from 'lucide-vue-next';

    import EfficiencyGauge from './components/EfficiencyGauge.vue';
    import CarbonChart from './components/CarbonChart.vue';
    import AppSidebar from './components/AppSidebar.vue';
    import LoginPage from './components/LoginPage.vue';

    const isLoggedIn = ref(false);
    const isSidebarOpen = ref(false);
    const isCollapsed = ref(false);
    const isModalOpen = ref(false);
    const isProfileOpen = ref(false);
    const isMobile = ref(window.innerWidth < 1024);

    // Resize listener
    onMounted(() => {
        window.addEventListener('resize', () => {
            isMobile.value = window.innerWidth < 1024;
            if (!isMobile.value) isSidebarOpen.value = true;
        });
    });

    // DATA STATE
    const machines = ref([
        { id: 'CRC-001A', name: 'Zoh Shia', location: 'Hafnarfjörður, Iceland' },
        { id: 'CRC-002Z', name: 'EVA-002', location: "St. John's, Canada" },
        { id: 'CRC-003X', name: 'Sahara Sol', location: 'Gibraltar, Morocco' },
    ]);
    const selectedMachineId = ref('CRC-001A');
    const newMachine = reactive({ name: '', id: '' });

    const dummyData = reactive({
        totalCO2: 1254.7,
        carbonUsed: 231.2,
        netImpact: 1023.5,
        co2PerHour: 1.57,
        mineralOutput: 3.21,
        mineralTarget: 5,
        efficiencyAI: 94,
    });
    const efficiencyTrend = ref<'up'|'down'>('up');
    const historicalData = ref(Array.from({length: 30}, () => Math.floor(Math.random() * (60 - 30) + 30)));

    const selectedMachine = computed(() => machines.value.find(m => m.id === selectedMachineId.value));
    const mineralProgress = computed(() => (dummyData.mineralOutput / dummyData.mineralTarget) * 100);

    // ACTIONS
    const handleLoginSuccess = () => {
        isLoggedIn.value = true;
    };

    const handleLogout = () => {
        isLoggedIn.value = false;
        isProfileOpen.value = false;
        isSidebarOpen.value = false;
    };

    const selectMachine = (id: string) => {
        selectedMachineId.value = id;
        if (isMobile.value) isSidebarOpen.value = false;
        
        // Simulate data refresh
        dummyData.efficiencyAI = Math.floor(Math.random() * 30) + 70;
        dummyData.co2PerHour = parseFloat((Math.random() * 2 + 0.5).toFixed(2));
        efficiencyTrend.value = Math.random() > 0.5 ? 'up' : 'down';
        historicalData.value = Array.from({length: 30}, () => Math.floor(Math.random() * (60 - 30) + 30));
    };

    const addMachine = () => {
        if(!newMachine.name || !newMachine.id) return;
        machines.value.push({
            id: newMachine.id,
            name: newMachine.name,
            location: 'New Location'
        });
        selectMachine(newMachine.id);
        closeModal();
    };

    const closeModal = () => {
        isModalOpen.value = false;
        isProfileOpen.value = false;
        newMachine.name = '';
        newMachine.id = '';
    };
</script>

<template>
  <div class="h-screen overflow-hidden bg-gray-50 text-slate-800 font-sans">
    
    <!-- VIEW: LOGIN PAGE -->
    <transition name="fade" mode="out-in">
      <div v-if="!isLoggedIn" class="h-full w-full">
        <LoginPage @login-success="handleLoginSuccess" />
      </div>

      <!-- VIEW: DASHBOARD -->
      <div v-else class="flex h-full w-full">
        <!-- Mobile Overlay -->
        <div v-if="isMobile && isSidebarOpen" 
             @click="isSidebarOpen = false"
             class="fixed inset-0 bg-black/50 z-20 backdrop-blur-sm lg:hidden">
        </div>

        <AppSidebar 
          :machines="machines"
          :selectedMachineId="selectedMachineId"
          :isCollapsed="isCollapsed"
          :isMobile="isMobile"
          :isSidebarOpen="isSidebarOpen"
          :isLoggedIn="isLoggedIn"
          @toggleSidebar="isSidebarOpen = !isSidebarOpen"
          @toggleCollapse="isCollapsed = !isCollapsed"
          @selectMachine="selectMachine"
          @openModal="isModalOpen = true"
          @openProfile="isProfileOpen = true"
          @login="isLoggedIn = true"
          @logout="handleLogout"
        />

        <main class="flex-1 flex flex-col h-screen overflow-hidden relative w-full">
            <!-- MOBILE HEADER -->
            <header class="lg:hidden h-16 bg-white border-b flex items-center justify-between px-4 shrink-0 shadow-sm z-10">
                <button @click="isSidebarOpen = !isSidebarOpen" class="p-2 -ml-2 text-slate-600">
                    <Menu class="w-6 h-6" />
                </button>
                <span class="font-bold text-slate-800">M-CORA</span>
                <div class="w-8"></div>
            </header>

            <div class="flex-1 overflow-y-auto p-4 md:p-8">
                <div class="max-w-7xl mx-auto space-y-6">
                    
                    <!-- DASHBOARD HEADER -->
                    <div class="flex flex-col md:flex-row md:items-end justify-between gap-4">
                        <div>
                            <h2 class="text-2xl md:text-3xl font-bold text-slate-900">{{ selectedMachine?.name }}</h2>
                            <div class="flex items-center gap-2 text-slate-500 text-sm mt-1">
                                <span class="bg-green-100 text-green-700 px-2 py-0.5 rounded text-xs font-semibold">ONLINE</span>
                                <span>•</span>
                                <span>Updated just now</span>
                            </div>
                        </div>
                        <div class="text-right hidden md:block">
                             <span class="text-xs font-mono text-slate-400">ID: {{ selectedMachine?.id }}</span>
                        </div>
                    </div>

                    <!-- SECTION 1: HEADLINE -->
                    <section class="grid grid-cols-1 md:grid-cols-3 gap-6">
                        <div class="md:col-span-2 bg-white rounded-2xl p-6 shadow-sm border border-slate-100 relative overflow-hidden group">
                            <div class="absolute top-0 right-0 p-4 opacity-10 group-hover:opacity-20 transition-opacity">
                                <Leaf class="w-32 h-32 text-emerald-500" />
                            </div>
                            <div class="relative z-10">
                                <div class="text-sm font-medium text-slate-500 uppercase tracking-wide mb-2">Total CO2 Credit</div>
                                <div class="flex items-baseline gap-2">
                                    <span class="text-5xl md:text-6xl font-bold text-slate-900 tracking-tight">{{ dummyData.totalCO2.toLocaleString() }}</span>
                                    <span class="text-xl text-slate-500 font-medium">T</span>
                                </div>
                                <div class="mt-4 flex items-center gap-2 text-sm text-emerald-600 bg-emerald-50 w-fit px-3 py-1 rounded-full border border-emerald-100">
                                    <TrendingUp class="w-4 h-4" />
                                    <span>+12.5% this month</span>
                                </div>
                            </div>
                        </div>

                        <div class="bg-white rounded-2xl p-6 shadow-sm border border-slate-100 flex flex-col justify-center">
                            <div class="text-sm font-medium text-slate-500 uppercase tracking-wide mb-3">Net Carbon Impact</div>
                            <div class="flex items-center gap-4 mb-4">
                                <div :class="['p-3 rounded-xl', dummyData.netImpact >= 0 ? 'bg-emerald-100 text-emerald-600' : 'bg-red-100 text-red-600']">
                                    <component :is="dummyData.netImpact >= 0 ? ShieldCheck : AlertCircle" class="w-8 h-8" />
                                </div>
                                <div>
                                    <div :class="['text-3xl font-bold', dummyData.netImpact >= 0 ? 'text-emerald-600' : 'text-red-600']">
                                        {{ dummyData.netImpact > 0 ? '+' : '' }}{{ dummyData.netImpact.toFixed(1) }} <span class="text-lg">T</span>
                                    </div>
                                    <div class="text-xs text-slate-400">Removed vs. Process Usage</div>
                                </div>
                            </div>
                        </div>
                    </section>

                    <!-- SECTION 2: REAL-TIME -->
                    <section class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                        <div class="bg-white rounded-2xl p-6 shadow-sm border border-slate-100 flex flex-col justify-between">
                            <div>
                                <h3 class="font-semibold text-slate-700 mb-4">CO2 Captured per Hour</h3>
                                <div class="flex items-end justify-between mb-2">
                                    <div class="flex items-baseline gap-2">
                                        <span class="text-4xl font-extrabold text-blue-600">{{ dummyData.co2PerHour }}</span>
                                        <span class="text-lg font-medium text-slate-500">T/hr</span>
                                    </div>
                                    
                                    <div class="text-right mb-1">
                                        <div class="font-bold text-emerald-600 text-sm">+3.1%</div>
                                        <div class="text-xs text-slate-400">vs. last hour</div>
                                    </div>
                                </div>
                            </div>

                            <div class="flex gap-1 h-8 items-end mt-4">
                                <div v-for="n in 12" :key="n" 
                                     class="flex-1 bg-blue-500 rounded-sm transition-all duration-500 hover:bg-blue-600"
                                     :class="n === 12 ? 'opacity-100' : 'opacity-40'"
                                     :style="{ height: Math.max(20, Math.random() * 100) + '%' }"></div>
                            </div>
                        </div>

                        <div class="bg-white rounded-2xl p-6 shadow-sm border border-slate-100">
                            <h3 class="font-semibold text-slate-700 mb-6">Mineral Output (Daily)</h3>
                            <div class="flex justify-between items-center mb-2">
                                <span class="text-3xl font-bold text-amber-600">{{ dummyData.mineralOutput }}</span>
                                <span class="text-sm text-slate-400">/ {{ dummyData.mineralTarget }} T Target</span>
                            </div>
                            <div class="w-full bg-slate-100 rounded-full h-3 mb-4">
                                <div class="bg-amber-500 h-3 rounded-full transition-all duration-1000" 
                                     :style="{ width: mineralProgress + '%' }"></div>
                            </div>
                            <div class="flex gap-3 text-sm">
                                <div class="flex items-center gap-1.5 text-slate-600">
                                    <Box class="w-4 h-4 text-amber-500" /> <span>CaCO3</span>
                                </div>
                                <div class="flex items-center gap-1.5 text-slate-600">
                                    <Box class="w-4 h-4 text-slate-400" /> <span>MgCO3</span>
                                </div>
                            </div>
                        </div>

                        <EfficiencyGauge :score="dummyData.efficiencyAI" :trend="efficiencyTrend" />
                    </section>

                    <!-- SECTION 3: CUMULATIVE -->
                    <section class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                        <div class="space-y-6">
                            <div class="bg-white rounded-2xl p-6 shadow-sm border border-slate-100 flex items-center gap-5">
                                <div class="w-14 h-14 rounded-2xl bg-teal-50 flex items-center justify-center text-teal-600">
                                    <Sprout class="w-7 h-7" />
                                </div>
                                <div>
                                    <div class="text-2xl font-bold text-slate-900">{{ (dummyData.totalCO2 * 0.82).toLocaleString(undefined, {maximumFractionDigits:0}) }} T</div>
                                    <div class="text-sm text-slate-500">Green Material Harvested</div>
                                </div>
                            </div>

                            <div class="bg-white rounded-2xl p-6 shadow-sm border border-slate-100 flex items-center gap-5">
                                <div class="w-14 h-14 rounded-2xl bg-blue-50 flex items-center justify-center text-blue-600">
                                    <Droplet class="w-7 h-7" />
                                </div>
                                <div>
                                    <div class="text-2xl font-bold text-slate-900">{{ (dummyData.totalCO2 * 0.045 * 1000).toLocaleString(undefined, {maximumFractionDigits:0}) }} kg</div>
                                    <div class="text-sm text-slate-500">Hydrogen Produced</div>
                                </div>
                            </div>
                        </div>

                        <div class="lg:col-span-2 bg-white rounded-2xl p-6 shadow-sm border border-slate-100 flex flex-col">
                            <div class="flex items-center justify-between mb-6">
                                <div>
                                    <h3 class="font-semibold text-slate-700">CO2 Removed History</h3>
                                    <p class="text-xs text-slate-400">Last 30 Days Performance</p>
                                </div>
                            </div>
                            <div class="flex-1 w-full min-h-[250px] relative">
                                <CarbonChart :data="historicalData" />
                            </div>
                        </div>
                    </section>

                     <!-- SECTION 4: HARDWARE -->
                     <section class="grid grid-cols-1 md:grid-cols-2 gap-6 pb-6">
                        <div class="bg-white rounded-2xl p-6 shadow-sm border border-slate-100">
                            <h3 class="font-semibold text-slate-700 mb-4">Hardware Status</h3>
                            <div class="space-y-4">
                                <div class="flex justify-between border-b border-slate-50 pb-2">
                                    <span class="text-slate-500">Unit ID</span>
                                    <span class="font-mono text-slate-800">{{ selectedMachine?.id }}</span>
                                </div>
                                <div class="flex justify-between border-b border-slate-50 pb-2">
                                    <span class="text-slate-500">Firmware</span>
                                    <span class="text-slate-800">v4.5.2-stable</span>
                                </div>
                                <div class="flex justify-between items-center">
                                    <span class="text-slate-500">Connection</span>
                                    <span class="flex items-center gap-2 text-emerald-600 text-sm font-medium">
                                        <span class="relative flex h-2.5 w-2.5">
                                          <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
                                          <span class="relative inline-flex rounded-full h-2.5 w-2.5 bg-emerald-500"></span>
                                        </span>
                                        Stable 5ms
                                    </span>
                                </div>
                            </div>
                        </div>
                        
                        <div class="bg-slate-200 rounded-2xl overflow-hidden relative min-h-[200px] flex items-center justify-center group">
                            <div class="absolute inset-0 bg-[url('https://upload.wikimedia.org/wikipedia/commons/e/ec/World_map_blank_without_borders.svg')] bg-cover opacity-10"></div>
                            <div class="relative z-10 text-center">
                                <div class="w-12 h-12 bg-white rounded-full shadow-lg flex items-center justify-center mx-auto mb-2 text-indigo-600 animate-bounce">
                                    <MapPin class="w-6 h-6" />
                                </div>
                                <p class="font-semibold text-slate-700">{{ selectedMachine?.location }}</p>
                                <p class="text-xs text-slate-500">64.9631° N, 19.0208° W</p>
                            </div>
                        </div>
                    </section>
                </div>
            </div>
        </main>
      </div>
    </transition>

    <!-- MODAL: ADD MACHINE -->
    <transition name="fade">
        <div v-if="isModalOpen" class="fixed inset-0 z-50 flex items-center justify-center px-4">
            <div class="absolute inset-0 bg-slate-900/70 backdrop-blur-sm" @click="closeModal"></div>
            <div class="bg-white rounded-2xl shadow-2xl w-full max-w-md relative z-10 overflow-hidden">
                <div class="bg-slate-50 px-6 py-4 border-b border-slate-100 flex justify-between items-center">
                    <h3 class="font-bold text-lg text-slate-800">Add New Unit</h3>
                    <button @click="closeModal" class="text-slate-400 hover:text-slate-600 cursor-pointer">
                        <X class="w-5 h-5" />
                    </button>
                </div>
                <form @submit.prevent="addMachine" class="p-6 space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">Machine Name</label>
                        <input type="text" v-model="newMachine.name" placeholder="e.g. Borealis-X" required
                               class="w-full px-4 py-2 rounded-lg border border-slate-300 focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">Unit ID</label>
                        <input type="text" v-model="newMachine.id" placeholder="e.g. M-CR-99" required
                               class="w-full px-4 py-2 rounded-lg border border-slate-300 focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all uppercase">
                    </div>
                    <div class="pt-4 flex gap-3">
                        <button type="button" @click="closeModal" class="flex-1 py-2.5 border border-slate-300 rounded-lg text-slate-600 hover:bg-slate-50 transition-colors cursor-pointer">Cancel</button>
                        <button type="submit" class="flex-1 py-2.5 bg-emerald-600 text-white rounded-lg hover:bg-emerald-700 transition-colors shadow-lg cursor-pointer">Register Unit</button>
                    </div>
                </form>
            </div>
        </div>
    </transition>

    <!-- MODAL: USER PROFILE -->
    <transition name="fade">
        <div v-if="isProfileOpen" class="fixed inset-0 z-50 flex items-center justify-center px-4">
            <div class="absolute inset-0 bg-slate-900/70 backdrop-blur-sm" @click="closeModal"></div>
            <div class="bg-white rounded-2xl shadow-2xl w-full max-w-sm relative z-10 overflow-hidden">
                <div class="bg-indigo-600 p-6 text-center text-white">
                    <div class="w-20 h-20 rounded-full bg-white/20 mx-auto flex items-center justify-center text-3xl font-bold mb-3 border-4 border-white/30">
                        JD
                    </div>
                    <h3 class="font-bold text-lg">John Doe</h3>
                    <p class="text-indigo-200 text-sm">Operator Level 3</p>
                </div>
                <div class="p-4 space-y-2">
                    <button class="w-full flex items-center gap-3 p-3 rounded-lg hover:bg-slate-50 transition-colors text-slate-700">
                         <User class="w-5 h-5 text-slate-400" /> Account Settings
                    </button>
                    <div class="h-px bg-slate-100 my-2"></div>
                    <button @click="handleLogout" class="w-full flex items-center gap-3 p-3 rounded-lg hover:bg-red-50 text-red-600 transition-colors">
                         <X class="w-5 h-5" /> Sign Out
                    </button>
                </div>
            </div>
        </div>
    </transition>
  </div>
</template>

<style>
    /* Transition stuff */
    .fade-enter-active, .fade-leave-active { transition: opacity 0.3s ease; }
    .fade-enter-from, .fade-leave-to { opacity: 0; }
</style>