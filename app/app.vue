<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue';
import { Github, Linkedin, Mail, Coffee, Cpu, Terminal, Zap, ArrowUp, Code2, Send, Star } from 'lucide-vue-next';

// --- Tools Data ---
const tools = [
  { name: 'C++ 17/20', icon: Code2 },
  { name: 'C (Embedded)', icon: Cpu },
  { name: 'Rust', icon: Zap },
  { name: 'Linux/Bash', icon: Terminal },
  { name: 'CMake', icon: '⚙️' },
  { name: 'GDB / Valgrind', icon: '🐞' },
  { name: 'Git', icon: '🌳' },
  { name: 'Python', icon: '🐍' },
];

// --- State Management ---
const scrolled = ref(false);
const heatLevel = ref(2); // Start at "HOT" (Level 2)
const isBlinking = ref(false);

// --- Blinking Logic ---
onMounted(() => {
  const handleScroll = () => {
    scrolled.value = window.scrollY > 50;
  };
  window.addEventListener('scroll', handleScroll);

  // Random blink loop
  let timeoutId;
  const blinkLoop = () => {
    const timeToNextBlink = Math.random() * 3000 + 2000;
    timeoutId = setTimeout(() => {
      isBlinking.value = true;
      setTimeout(() => {
        isBlinking.value = false;
        blinkLoop();
      }, 150);
    }, timeToNextBlink);
  };
  blinkLoop();

  onUnmounted(() => {
    window.removeEventListener('scroll', handleScroll);
    clearTimeout(timeoutId);
  });
});

const scrollTo = (id) => {
  const el = document.getElementById(id);
  if (el) el.scrollIntoView({ behavior: 'smooth' });
};

const handleMugClick = () => {
  // Cycle: Empty (0) -> Cold (1) -> Hot (2) -> Boiling (3)
  heatLevel.value = (heatLevel.value + 1) % 4;
};

// Computed Properties for Styles
const isEmpty = computed(() => heatLevel.value === 0);
const isSteaming = computed(() => heatLevel.value >= 2);
const isBoiling = computed(() => heatLevel.value === 3);
const liquidColor = computed(() => isEmpty.value ? "#e7e5e4" : "#431407");
const labelText = computed(() => ["REFILL?", "COLD", "HOT", "BOILING"][heatLevel.value]);
const steamDuration = computed(() => isBoiling.value ? "0.5s" : "2s");
</script>

<template>
  <div class="min-h-screen bg-[#fff7ed] font-sans text-[#431407] relative overflow-x-hidden selection:bg-[#d97706] selection:text-white">
    
    <!-- Coffee Stain Backgrounds -->
    <div class="fixed inset-0 pointer-events-none opacity-10">
      <div class="absolute top-[-50px] right-[-50px] w-64 h-64 rounded-full border-[20px] border-[#431407] blur-sm"></div>
      <div class="absolute bottom-[20%] left-[10%] w-48 h-48 rounded-full border-[15px] border-[#431407] blur-sm"></div>
    </div>

    <!-- Navigation -->
    <nav :class="['fixed w-full z-50 transition-all duration-300', scrolled ? 'bg-[#fff7ed]/95 backdrop-blur-md shadow-sm py-4' : 'bg-transparent py-8']">
      <div class="container mx-auto px-6 flex justify-between items-center">
        <div 
          class="font-serif font-black text-2xl text-[#431407] flex items-center gap-2 cursor-pointer uppercase tracking-tight"
          @click="scrollTo('home')"
        >
          <Coffee class="w-6 h-6 text-[#d97706]" />
          Sebastian Prokop
        </div>
        
        <!-- Inspiration Style Links -->
        <div class="hidden md:flex gap-6 font-mono text-sm font-bold items-center">
          <button @click="scrollTo('work')" class="hover:text-[#d97706] transition-colors">whoami // projects</button>
          <button @click="scrollTo('tools')" class="hover:text-[#d97706] transition-colors">machine</button>
          <button @click="scrollTo('contact')" class="hover:text-[#d97706] transition-colors">contact</button>
        </div>

        <button @click="scrollTo('contact')" class="md:hidden p-2 bg-[#431407] text-white rounded-md">
          <Mail class="w-5 h-5" />
        </button>
      </div>
    </nav>

    <!-- Hero Section -->
    <section id="home" class="pt-40 pb-20 px-6 relative z-10 min-h-[90vh] flex items-center">
      <div class="container mx-auto flex flex-col md:flex-row items-center justify-center gap-12">
        <div class="text-center md:text-left max-w-lg">
          <div class="inline-block px-4 py-1 bg-[#431407] text-[#fff7ed] rounded-sm text-xs font-bold mb-6 tracking-wide shadow-sm transform -rotate-2">
            ☕️ SYSTEMS ENGINEER
          </div>
          <h1 class="font-serif text-5xl md:text-7xl text-[#431407] mb-6 leading-[1.1] font-black">
            Hey! I'm <br/>
            <span class="text-[#d97706]">Sebastian.</span>
          </h1>
          <p class="text-xl text-[#78350f] mb-8 leading-relaxed font-medium">
            Specializing in C/C++, Embedded Systems, and high-performance backend architecture. No bloat, just pure caffeine.
          </p>
          <div class="flex flex-col sm:flex-row gap-4 justify-center md:justify-start">
            <button @click="scrollTo('work')" class="px-8 py-4 bg-[#431407] text-white font-bold rounded-sm hover:bg-[#5d4037] hover:shadow-lg hover:-translate-y-1 transition-all uppercase tracking-wide">
              Check the Menu
            </button>
            <button @click="scrollTo('contact')" class="px-8 py-4 bg-transparent text-[#431407] border-2 border-[#431407] font-bold rounded-sm hover:bg-[#fffbeb] hover:-translate-y-1 transition-all uppercase tracking-wide">
              Grab a Coffee
            </button>
          </div>
        </div>
        
        <!-- Happy Mug SVG -->
        <svg 
          @click="handleMugClick"
          viewBox="0 0 200 200" 
          class="w-64 h-64 md:w-80 md:h-80 drop-shadow-xl hover:-rotate-3 transition-transform cursor-pointer origin-bottom select-none"
        >
          <g :style="{ opacity: isSteaming ? 1 : 0, transition: 'opacity 0.5s ease' }">
            <path d="M70 40 Q80 20 70 0" fill="none" stroke="#d6d3d1" stroke-width="4" stroke-linecap="round" class="animate-pulse" :style="{ animationDelay: '0s', animationDuration: steamDuration }" />
            <path d="M100 30 Q110 10 100 -10" fill="none" stroke="#d6d3d1" stroke-width="4" stroke-linecap="round" class="animate-pulse" :style="{ animationDelay: '0.5s', animationDuration: steamDuration }" />
            <path d="M130 40 Q140 20 130 0" fill="none" stroke="#d6d3d1" stroke-width="4" stroke-linecap="round" class="animate-pulse" :style="{ animationDelay: '1s', animationDuration: steamDuration }" />
          </g>
          <path d="M50 60 L150 60 L140 160 Q100 180 60 160 L50 60 Z" fill="#fff7ed" stroke="#431407" stroke-width="4" />
          <path d="M150 80 Q180 80 180 110 Q180 140 145 140" fill="none" stroke="#431407" stroke-width="8" stroke-linecap="round" />
          <path d="M150 80 Q180 80 180 110 Q180 140 145 140" fill="none" stroke="#fff7ed" stroke-width="4" stroke-linecap="round" />
          <ellipse cx="100" cy="60" rx="50" ry="10" :fill="liquidColor" style="transition: fill 0.5s ease" />
          
          <g>
            <template v-if="isBlinking">
              <line x1="80" y1="110" x2="90" y2="110" stroke="#431407" stroke-width="3" stroke-linecap="round" />
              <line x1="110" y1="110" x2="120" y2="110" stroke="#431407" stroke-width="3" stroke-linecap="round" />
            </template>
            <template v-else>
              <circle cx="85" cy="110" r="4" fill="#431407" />
              <circle cx="115" cy="110" r="4" fill="#431407" />
            </template>
            <circle v-if="isBoiling" cx="100" cy="120" r="6" fill="#431407" />
            <path v-else d="M95 115 Q100 120 105 115" fill="none" stroke="#431407" stroke-width="2" stroke-linecap="round" />
            <ellipse cx="80" cy="118" rx="4" ry="2" fill="#fda4af" opacity="0.6" />
            <ellipse cx="120" cy="118" rx="4" ry="2" fill="#fda4af" opacity="0.6" />
          </g>

          <rect x="70" y="135" width="60" height="15" rx="2" fill="#d97706" />
          <text x="100" y="146" font-size="8" text-anchor="middle" fill="white" font-weight="bold" font-family="sans-serif">{{ labelText }}</text>
        </svg>
      </div>
    </section>

    <!-- Projects Section -->
    <section id="work" class="py-24 px-6 relative z-10 bg-[#fafaf9] border-t border-[#e7e5e4]">
      <div class="container mx-auto">
        <div class="text-center mb-16">
          <h2 class="font-serif text-4xl font-black text-[#431407] mb-4">Daily Brews</h2>
          <p class="text-[#a8a29e] font-bold uppercase tracking-widest text-sm">Selected Technical Projects</p>
        </div>
        
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
          <!-- Project Card 1 -->
          <div class="bg-white p-8 rounded-lg border border-[#e7e5e4] shadow-sm hover:shadow-xl hover:-translate-y-1 transition-all duration-300 relative group overflow-hidden">
            <div class="absolute top-0 left-0 w-full h-1 bg-gradient-to-r from-[#d97706] to-[#431407]"></div>
            <div class="absolute -right-12 -top-12 w-24 h-24 bg-[#fff7ed] rounded-full group-hover:scale-150 transition-transform duration-500"></div>
            <div class="relative z-10">
              <div class="mb-6">
                <div class="flex justify-between items-start mb-2">
                  <h3 class="font-serif font-bold text-2xl text-[#431407]">Engine Core</h3>
                  <Star class="w-5 h-5 text-[#d97706]" fill="#d97706" />
                </div>
                <p class="text-xs font-bold uppercase tracking-widest text-[#a8a29e]">Custom Game Engine</p>
              </div>
              <p class="text-[#5d4037] leading-relaxed text-sm md:text-base mb-6 min-h-[80px]">
                A 2D rendering engine written in pure C++ and OpenGL. Handles 10,000+ sprites at 60FPS with custom memory allocators.
              </p>
              <div class="flex flex-wrap gap-2 pt-4 border-t border-[#f5f5f4]">
                <span v-for="tag in ['C++', 'OpenGL', 'CMake']" :key="tag" class="tag">{{ tag }}</span>
              </div>
            </div>
          </div>

          <!-- Project Card 2 -->
          <div class="bg-white p-8 rounded-lg border border-[#e7e5e4] shadow-sm hover:shadow-xl hover:-translate-y-1 transition-all duration-300 relative group overflow-hidden">
             <div class="absolute top-0 left-0 w-full h-1 bg-gradient-to-r from-[#d97706] to-[#431407]"></div>
             <div class="absolute -right-12 -top-12 w-24 h-24 bg-[#fff7ed] rounded-full group-hover:scale-150 transition-transform duration-500"></div>
             <div class="relative z-10">
              <div class="mb-6">
                <div class="flex justify-between items-start mb-2">
                  <h3 class="font-serif font-bold text-2xl text-[#431407]">RTOS Controller</h3>
                  <Star class="w-5 h-5 text-[#d97706]" fill="#d97706" />
                </div>
                <p class="text-xs font-bold uppercase tracking-widest text-[#a8a29e]">Embedded Systems</p>
              </div>
              <p class="text-[#5d4037] leading-relaxed text-sm md:text-base mb-6 min-h-[80px]">
                Firmware for an ARM Cortex-M4 microcontroller. Features a custom task scheduler and I2C sensor drivers.
              </p>
              <div class="flex flex-wrap gap-2 pt-4 border-t border-[#f5f5f4]">
                <span v-for="tag in ['C', 'FreeRTOS', 'STM32']" :key="tag" class="tag">{{ tag }}</span>
              </div>
            </div>
          </div>

          <!-- Project Card 3 -->
          <div class="bg-white p-8 rounded-lg border border-[#e7e5e4] shadow-sm hover:shadow-xl hover:-translate-y-1 transition-all duration-300 relative group overflow-hidden">
             <div class="absolute top-0 left-0 w-full h-1 bg-gradient-to-r from-[#d97706] to-[#431407]"></div>
             <div class="absolute -right-12 -top-12 w-24 h-24 bg-[#fff7ed] rounded-full group-hover:scale-150 transition-transform duration-500"></div>
             <div class="relative z-10">
              <div class="mb-6">
                <div class="flex justify-between items-start mb-2">
                  <h3 class="font-serif font-bold text-2xl text-[#431407]">NetProxy</h3>
                  <Star class="w-5 h-5 text-[#d97706]" fill="#d97706" />
                </div>
                <p class="text-xs font-bold uppercase tracking-widest text-[#a8a29e]">Networking Tool</p>
              </div>
              <p class="text-[#5d4037] leading-relaxed text-sm md:text-base mb-6 min-h-[80px]">
                A multi-threaded HTTP proxy server written from scratch. Implements thread pooling and LRU caching for high throughput.
              </p>
              <div class="flex flex-wrap gap-2 pt-4 border-t border-[#f5f5f4]">
                <span v-for="tag in ['C++20', 'Linux', 'Sockets']" :key="tag" class="tag">{{ tag }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Skills Section -->
    <section id="tools" class="py-20 px-6 relative z-10">
      <div class="container mx-auto max-w-4xl">
         <div class="text-center mb-12">
          <h2 class="font-serif text-3xl font-black text-[#431407] mb-4 flex items-center justify-center gap-3">
            <Terminal class="w-8 h-8 text-[#d97706]" />
            The Barista Kit
          </h2>
          <p class="text-[#78350f]">My technical equipment.</p>
        </div>
        <div class="bg-[#292524] text-[#d6d3d1] p-8 rounded-lg shadow-2xl relative overflow-hidden">
           <div class="absolute top-0 left-0 w-full h-2 bg-gradient-to-r from-[#d97706] to-[#431407]"></div>
           
          <div class="grid grid-cols-2 md:grid-cols-4 gap-8 text-center font-mono text-sm">
            <div v-for="tool in tools" :key="tool.name" class="p-2 hover:bg-[#44403c] rounded transition-colors cursor-default group">
              <component :is="tool.icon" v-if="typeof tool.icon !== 'string'" class="mx-auto mb-2 text-[#d97706] group-hover:scale-110 transition-transform" />
              <div v-else class="text-xl mb-2 group-hover:scale-110 transition-transform">{{ tool.icon }}</div>
              <div class="font-bold mt-2">{{ tool.name }}</div>
            </div>
          </div>
          <div class="mt-8 text-center text-xs text-[#a8a29e] border-t border-[#44403c] pt-4">
             &gt; System Status: OPTIMIZED
          </div>
        </div>
      </div>
    </section>

    <!-- Contact Section -->
    <section id="contact" class="py-24 px-6 relative z-10">
      <div class="container mx-auto max-w-2xl text-center">
        <div class="bg-white border-4 border-[#431407] p-8 md:p-12 shadow-[8px_8px_0_0_#431407] relative transform rotate-1">
          <div class="absolute top-[-20px] right-[-20px] w-24 h-24 rounded-full border-[6px] border-[#431407] opacity-10 pointer-events-none"></div>
          
          <h2 class="font-serif text-4xl font-black text-[#431407] mb-6 mt-4">Place an Order</h2>
          <p class="text-[#5d4037] mb-8 text-lg font-medium">
            Need a Systems Engineer for your next high-performance project? My inbox is always open for new opportunities.
          </p>
          
          <div class="flex flex-col gap-4">
            <a href="mailto:hello@example.com" class="group flex items-center justify-center gap-3 w-full p-4 bg-[#431407] text-[#fff7ed] font-bold text-lg rounded-sm hover:bg-[#5d4037] hover:-translate-y-1 transition-all uppercase tracking-widest">
              <Send class="w-5 h-5 group-hover:translate-x-1 transition-transform" />
              hello@systemsbrew.com
            </a>
            <div class="flex gap-4 justify-center mt-4">
              <a href="#" class="p-3 bg-[#fff7ed] border-2 border-[#431407] text-[#431407] rounded-full hover:bg-[#ffedd5] transition-colors">
                <Github class="w-6 h-6" />
              </a>
              <a href="#" class="p-3 bg-[#fff7ed] border-2 border-[#431407] text-[#431407] rounded-full hover:bg-[#ffedd5] transition-colors">
                <Linkedin class="w-6 h-6" />
              </a>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Footer -->
    <footer class="py-8 text-center text-[#78350f] text-sm font-bold uppercase tracking-widest bg-[#fffbeb] border-t border-[#e7e5e4]">
      <p>© 2024 Sebastian Prokop. Powered by Caffeine & C++.</p>
      <button @click="scrollTo('home')" class="mt-4 flex items-center gap-2 mx-auto hover:text-[#431407]">
        Top up <ArrowUp class="w-4 h-4" />
      </button>
    </footer>
  </div>
</template>

<style scoped>
.tag {
  @apply text-xs font-bold text-[#78350f] bg-[#fff7ed] px-2 py-1 rounded-md border border-[#fed7aa];
}
</style>