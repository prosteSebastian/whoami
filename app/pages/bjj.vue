<template>
  <div>
    <!-- GRIMOIRE SCENE -->
    <div v-if="scene === 'grimoire'" class="min-h-screen bg-slate-950 flex flex-col items-center p-4 md:p-8 font-mono text-slate-300 selection:bg-indigo-500 selection:text-white">
      <div class="w-full max-w-4xl flex justify-between items-center bg-slate-900 border border-slate-800 p-4 rounded-lg mb-8 shadow-xl">
        <button @click="scene = 'map'" class="flex items-center gap-2 hover:text-white transition-colors text-sm">
          <ChevronLeft :size="16" /> Back to Map
        </button>
        <div class="text-xs text-slate-500 tracking-widest uppercase flex items-center gap-2">
          <BookOpen :size="14" class="text-indigo-500" /> Technique Archive
        </div>
      </div>

      <div class="max-w-4xl w-full">
        <div class="grid gap-3 pb-8">
          <div v-for="m in MOVES" :key="m.id" class="bg-slate-900 border border-slate-800 p-4 rounded-lg flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 hover:border-slate-700 transition-colors">
            <div>
              <div class="font-bold text-slate-200 text-lg">{{ m.name }}</div>
              <div class="text-[10px] sm:text-xs text-slate-500 mt-2 flex flex-wrap items-center gap-2">
                <span class="text-indigo-400 font-bold uppercase tracking-wider">{{ getPositionName(m.posId) }} ({{ m.side }})</span>
                <span class="hidden sm:inline">•</span>
                <span class="uppercase tracking-wider">{{ m.category }}</span>
                <span class="hidden sm:inline">•</span>
                <span :class="['px-1.5 py-0.5 rounded font-bold', getBeltColor(m.belt)]">{{ getBeltName(m.belt) }}</span>
              </div>
            </div>
            <button 
              @click="openYoutubeLink(m.name)"
              class="w-full sm:w-auto bg-slate-800 hover:bg-slate-700 text-slate-300 px-4 py-2.5 rounded text-xs font-bold uppercase tracking-widest flex items-center justify-center gap-2 transition-colors whitespace-nowrap border border-slate-700"
            >
              <Video :size="14" class="text-red-400" /> Watch
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- MAP SCENE -->
    <div v-else-if="scene === 'map'" class="min-h-screen bg-slate-950 flex flex-col items-center p-4 md:p-8 font-mono text-slate-300 selection:bg-indigo-500 selection:text-white">
      
      <!-- Header Controls -->
      <div class="w-full max-w-4xl flex justify-between items-center bg-slate-900 border border-slate-800 p-4 rounded-lg mb-12 shadow-xl">
        <div class="flex items-center gap-3">
          <Medal :size="20" class="text-indigo-400" />
          <select 
            v-model.number="playerBelt"
            class="bg-slate-950 border border-slate-700 text-sm font-bold rounded p-1.5 outline-none focus:border-indigo-500 text-white"
          >
            <option v-for="b in BELTS" :key="b.level" :value="b.level">{{ b.name }}</option>
          </select>
        </div>
        <button 
          @click="scene = 'grimoire'"
          class="flex items-center gap-2 bg-slate-800 hover:bg-slate-700 border border-slate-700 text-slate-300 px-3 py-1.5 rounded text-xs font-bold uppercase tracking-widest transition-colors"
        >
          <BookOpen :size="14" class="text-indigo-400" /> Grimoire
        </button>
      </div>

      <!-- Level Select -->
      <div class="max-w-3xl w-full">
        <h1 class="text-2xl font-bold mb-8 tracking-widest uppercase text-white flex items-center gap-3 border-b border-slate-800 pb-4">
          <MapIcon :size="24" class="text-indigo-500" /> Select Encounter
        </h1>

        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <button
            v-for="pos in POSITIONS" 
            :key="pos.id"
            @click="handleSelectPosition(pos.id, ['closed_guard', 'half_guard', 'open_guard'].includes(pos.id) ? 'bottom' : 'top')"
            class="bg-slate-900 border border-slate-800 p-5 rounded-lg text-left hover:border-indigo-500 hover:bg-slate-800 transition-all group flex items-center gap-4"
          >
            <div class="text-slate-600 group-hover:text-indigo-400 transition-colors">
              <component :is="pos.icon" :size="36" />
            </div>
            <div>
              <h3 class="font-bold text-slate-200 group-hover:text-white">{{ pos.name }}</h3>
              <p class="text-xs text-slate-500 mt-1">{{ pos.desc }}</p>
            </div>
          </button>
        </div>
      </div>
    </div>

    <!-- BATTLE SCENE -->
    <div v-else class="h-screen bg-slate-950 flex flex-col font-mono text-slate-300 overflow-hidden">
      
      <!-- TOP - BATTLEFIELD VISUAL -->
      <div class="flex-1 flex flex-col items-center justify-center relative p-8">
        <div class="absolute inset-0 bg-[radial-gradient(circle_at_center,_var(--tw-gradient-stops))] from-slate-900 to-slate-950"></div>
        
        <!-- HUD Info -->
        <div class="absolute top-6 left-6 flex flex-col gap-1 z-10">
          <div class="text-[10px] text-slate-500 tracking-widest uppercase">Location</div>
          <div class="font-bold text-white tracking-widest flex items-center gap-2">
            {{ activePosition?.name.toUpperCase() }}
          </div>
        </div>
        
        <div class="absolute top-6 right-6 flex flex-col items-end gap-1 z-10">
          <div class="text-[10px] text-slate-500 tracking-widest uppercase">Stance</div>
          <button 
            @click="handleSelectPosition(activePosId, activeSide === 'top' ? 'bottom' : 'top')"
            :class="['font-bold tracking-widest text-sm hover:underline cursor-pointer', activeSide === 'top' ? 'text-green-400' : 'text-orange-400']"
            title="Click to flip position"
          >
            {{ activeSide.toUpperCase() }} ⟳
          </button>
        </div>

        <!-- Center Minimalist Icon -->
        <div class="relative z-10 flex flex-col items-center group opacity-80">
          <div class="text-indigo-500/50 scale-150 mb-4 drop-shadow-[0_0_15px_rgba(99,102,241,0.3)]">
             <component :is="activePosition?.icon" :size="100" />
          </div>
        </div>
      </div>

      <!-- BOTTOM - TIGHTER RPG CONSOLE -->
      <div class="h-56 bg-slate-900 border-t border-slate-700 flex flex-col md:flex-row shadow-[0_-10px_30px_rgba(0,0,0,0.8)] z-20">
        
        <!-- Dialogue Box (Left) -->
        <div class="flex-1 p-5 md:p-6 border-r border-slate-800 relative flex flex-col">
          <div class="text-[10px] text-indigo-400/70 font-bold tracking-widest uppercase mb-2 flex items-center gap-2 shrink-0">
            <Terminal :size="12" /> System.Log
          </div>
          <div class="text-slate-300 text-sm md:text-base leading-relaxed overflow-y-auto whitespace-pre-wrap">{{ displayedText }}</div>
        </div>

        <!-- Command Menu (Right) -->
        <div class="w-full md:w-80 p-5 md:p-6 relative flex flex-col">
          <div class="text-[10px] text-indigo-400/70 font-bold tracking-widest uppercase mb-2 flex justify-between items-center shrink-0">
            <span>Commands</span>
            <span class="text-slate-600">Belt: {{ playerBelt }}</span>
          </div>

          <div class="flex-1 overflow-y-auto">
            
            <!-- STATE 1: Categories -->
            <template v-if="menuState === 'main'">
              <div v-if="availableCategories.length === 0" class="text-slate-500 text-sm mt-4">
                No commands available for this belt/stance.
              </div>
              <ul v-else class="space-y-1 mt-2">
                <li v-for="(cat, idx) in availableCategories" :key="idx">
                  <button
                    @click="handleSelectCategory(cat)"
                    class="w-full text-left px-3 py-1.5 rounded hover:bg-slate-800 text-slate-400 hover:text-white transition-colors group flex items-center gap-2 text-sm"
                  >
                    <ChevronsRight :size="14" class="opacity-0 group-hover:opacity-100 text-indigo-500 -ml-2 transition-all" />
                    {{ cat }}
                  </button>
                </li>
                <li class="mt-4 pt-4 border-t border-slate-800">
                  <button @click="scene = 'map'" class="w-full text-left px-3 py-1.5 text-xs text-slate-500 hover:text-slate-300">
                    [ Escape to Map ]
                  </button>
                </li>
              </ul>
            </template>

            <!-- STATE 2: Moves -->
            <template v-else-if="menuState === 'category'">
              <div class="flex flex-col h-full">
                <ul class="space-y-1 mt-2 flex-1">
                  <li v-for="move in categoryMoves" :key="move.id">
                    <button
                      @click="handleSelectMove(move)"
                      class="w-full text-left px-3 py-1.5 rounded hover:bg-slate-800 text-slate-300 hover:text-white transition-colors group flex items-center gap-2 text-sm"
                    >
                      <ChevronsRight :size="14" class="opacity-0 group-hover:opacity-100 text-indigo-500 -ml-2 transition-all" />
                      {{ move.name }}
                    </button>
                  </li>
                </ul>
                <button 
                  @click="handleBackToMain"
                  class="mt-2 text-xs text-slate-500 hover:text-slate-300 px-3 py-1 text-left w-fit"
                >
                  &lt; Back
                </button>
              </div>
            </template>

            <!-- STATE 3: Action Result (Practice Flow) -->
            <template v-else-if="menuState === 'result'">
              <div class="flex flex-col gap-2 mt-2">
                <button 
                  @click="openYoutubeLink(activeMove.name)"
                  class="w-full bg-slate-800 hover:bg-slate-700 text-slate-200 py-2 px-3 rounded text-xs flex items-center justify-center gap-2 transition-colors"
                >
                  <Video :size="14" class="text-red-400" /> Watch Tape
                </button>
                
                <button v-if="activeMove?.isSub"
                  @click="executeTransition"
                  class="w-full bg-indigo-600/20 hover:bg-indigo-600/40 text-indigo-400 py-2 px-3 rounded text-xs border border-indigo-500/30 transition-colors"
                >
                  Reset Drill (To Map)
                </button>

                <button v-else-if="activeMove?.transition"
                  @click="executeTransition"
                  class="w-full bg-green-600/20 hover:bg-green-600/40 text-green-400 py-2 px-3 rounded text-xs border border-green-500/30 flex justify-between items-center transition-colors"
                >
                  <span>Flow to {{ getPositionName(activeMove.transition.posId) }}</span>
                  <RefreshCcw :size="12" />
                </button>

                <button v-else
                  @click="handleBackToMain"
                  class="w-full bg-slate-800 hover:bg-slate-700 text-slate-200 py-2 px-3 rounded text-xs transition-colors"
                >
                  Continue from here
                </button>

                <button 
                  @click="handleBackToMain"
                  class="mt-1 text-xs text-slate-600 hover:text-slate-400 text-center"
                >
                  Cancel
                </button>
              </div>
            </template>

          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onUnmounted } from 'vue';
import { 
  Activity, ShieldAlert, User, Move, Crosshair, Swords,
  ChevronLeft, ChevronsRight, RefreshCcw, Map as MapIcon,
  Video, Medal, Terminal, BookOpen
} from 'lucide-vue-next';

// --- DATA STRUCTURES ---

const BELTS = [
  { id: 'white', name: 'White Belt', level: 1, color: 'bg-slate-200 text-slate-800' },
  { id: 'blue', name: 'Blue Belt', level: 2, color: 'bg-blue-600 text-white' },
  { id: 'purple', name: 'Purple Belt', level: 3, color: 'bg-purple-600 text-white' },
  { id: 'brown', name: 'Brown Belt', level: 4, color: 'bg-amber-800 text-white' },
  { id: 'black', name: 'Black Belt', level: 5, color: 'bg-slate-900 text-white border border-slate-700' }
];

const POSITIONS = [
  { id: 'standing', name: 'Standing', desc: 'Neutral feet. The match begins here.', icon: Activity },
  { id: 'closed_guard', name: 'Closed Guard', desc: 'Legs wrapped around the opponent.', icon: ShieldAlert },
  { id: 'half_guard', name: 'Half Guard', desc: 'Controlling one of the opponent\'s legs.', icon: User },
  { id: 'open_guard', name: 'Open Guard', desc: 'Using legs as frames to manage distance.', icon: Move },
  { id: 'mount', name: 'Mount', desc: 'Sitting on the opponent\'s torso.', icon: Crosshair },
  { id: 'back_control', name: 'Back Control', desc: 'Positioned behind the opponent with hooks.', icon: Swords }
];

const MOVES = [
  // STANDING (TOP = Offensive, BOTTOM = Defensive/Counter)
  { id: 1, posId: 'standing', side: 'top', category: 'Takedowns', belt: 1, name: 'Double Leg Takedown', desc: 'Drive through both legs to floor the opponent.', transition: { posId: 'half_guard', side: 'top' } },
  { id: 2, posId: 'standing', side: 'top', category: 'Takedowns', belt: 1, name: 'Single Leg Takedown', desc: 'Isolate one leg and sweep the other.', transition: { posId: 'half_guard', side: 'top' } },
  { id: 3, posId: 'standing', side: 'top', category: 'Takedowns', belt: 1, name: 'Osoto Gari', desc: 'Major outer reap from judo clinch.', transition: { posId: 'mount', side: 'top' } },
  { id: 4, posId: 'standing', side: 'top', category: 'Guard Pull', belt: 1, name: 'Pull Closed Guard', desc: 'Grab grips and pull them into your guard.', transition: { posId: 'closed_guard', side: 'bottom' } },
  
  { id: 5, posId: 'standing', side: 'bottom', category: 'Defense', belt: 1, name: 'Sprawl', desc: 'Drop hips heavy to defend a leg shot.', transition: { posId: 'back_control', side: 'top' } },
  { id: 6, posId: 'standing', side: 'bottom', category: 'Submissions', belt: 2, name: 'Standing Guillotine', desc: 'Catch the neck when they shoot in.', isSub: true },

  // CLOSED GUARD
  { id: 7, posId: 'closed_guard', side: 'bottom', category: 'Submissions', belt: 1, name: 'Triangle Choke', desc: 'Strangle them using your legs.', isSub: true },
  { id: 8, posId: 'closed_guard', side: 'bottom', category: 'Submissions', belt: 1, name: 'Armbar', desc: 'Hyperextend the elbow joint.', isSub: true },
  { id: 9, posId: 'closed_guard', side: 'bottom', category: 'Sweeps', belt: 1, name: 'Scissor Sweep', desc: 'Off-balance and chop their base.', transition: { posId: 'mount', side: 'top' } },
  { id: 10, posId: 'closed_guard', side: 'bottom', category: 'Sweeps', belt: 1, name: 'Hip Bump Sweep', desc: 'Sit up and bump them over.', transition: { posId: 'mount', side: 'top' } },
  
  { id: 11, posId: 'closed_guard', side: 'top', category: 'Passes', belt: 1, name: 'Tailbone Break', desc: 'Posture up and pry the guard open.', transition: { posId: 'open_guard', side: 'top' } },
  { id: 12, posId: 'closed_guard', side: 'top', category: 'Passes', belt: 1, name: 'Standing Break', desc: 'Stand up to force the guard open.', transition: { posId: 'open_guard', side: 'top' } },

  // MOUNT
  { id: 13, posId: 'mount', side: 'top', category: 'Submissions', belt: 1, name: 'Ezekiel Choke', desc: 'Sleeve choke from the top.', isSub: true },
  { id: 14, posId: 'mount', side: 'top', category: 'Submissions', belt: 1, name: 'Americana', desc: 'Paintbrush shoulder lock.', isSub: true },
  { id: 15, posId: 'mount', side: 'top', category: 'Submissions', belt: 1, name: 'Armbar', desc: 'Spin into an armbar from S-Mount.', isSub: true },

  { id: 16, posId: 'mount', side: 'bottom', category: 'Escapes', belt: 1, name: 'Upa (Bridge & Roll)', desc: 'Bridge and roll them over.', transition: { posId: 'closed_guard', side: 'top' } },
  { id: 17, posId: 'mount', side: 'bottom', category: 'Escapes', belt: 1, name: 'Elbow Escape (Shrimp)', desc: 'Shrimp out and catch the leg.', transition: { posId: 'half_guard', side: 'bottom' } },

  // BACK CONTROL
  { id: 18, posId: 'back_control', side: 'top', category: 'Submissions', belt: 1, name: 'Rear Naked Choke', desc: 'The ultimate submission.', isSub: true },
  { id: 19, posId: 'back_control', side: 'top', category: 'Submissions', belt: 2, name: 'Bow & Arrow Choke', desc: 'Use the collar and leg to strangle.', isSub: true },
  
  { id: 20, posId: 'back_control', side: 'bottom', category: 'Escapes', belt: 1, name: 'Slide Escape', desc: 'Slide hips to the non-choking side.', transition: { posId: 'half_guard', side: 'top' } },
  { id: 21, posId: 'back_control', side: 'bottom', category: 'Escapes', belt: 2, name: 'Spin to Guard', desc: 'Clear hooks and invert.', transition: { posId: 'closed_guard', side: 'bottom' } },

  // HALF GUARD
  { id: 22, posId: 'half_guard', side: 'bottom', category: 'Sweeps', belt: 1, name: 'Old School Sweep', desc: 'Grab the toe hold and sweep.', transition: { posId: 'mount', side: 'top' } },
  { id: 23, posId: 'half_guard', side: 'bottom', category: 'Submissions', belt: 1, name: 'Kimura', desc: 'Attack the posting arm.', isSub: true },
  
  { id: 24, posId: 'half_guard', side: 'top', category: 'Passes', belt: 1, name: 'Knee Slice Pass', desc: 'Cut the knee through.', transition: { posId: 'mount', side: 'top' } },
  { id: 25, posId: 'half_guard', side: 'top', category: 'Submissions', belt: 2, name: 'Darce Choke', desc: 'Shoot arm through armpit.', isSub: true },
  
  // OPEN GUARD
  { id: 26, posId: 'open_guard', side: 'bottom', category: 'Sweeps', belt: 1, name: 'Tripod Sweep', desc: 'Hook the heel and push the hip.', transition: { posId: 'mount', side: 'top' } },
  { id: 27, posId: 'open_guard', side: 'bottom', category: 'Submissions', belt: 1, name: 'Triangle Choke', desc: 'Shoot legs up for a choke.', isSub: true },

  { id: 28, posId: 'open_guard', side: 'top', category: 'Passes', belt: 1, name: 'Torreando Pass', desc: 'Throw the legs aside and pass.', transition: { posId: 'mount', side: 'top' } },
];

// --- REACTIVITY & STATE ---

const scene = ref('map');
const playerBelt = ref(1);
const activePosId = ref(null);
const activeSide = ref('bottom');
const menuState = ref('main');
const activeCategory = ref(null);
const activeMove = ref(null);
const dialogueText = ref("");
const displayedText = ref("");

// --- COMPUTED PROPERTIES ---

const activePosition = computed(() => POSITIONS.find(p => p.id === activePosId.value));

const availableBattleMoves = computed(() => 
  MOVES.filter(m => m.posId === activePosId.value && m.side === activeSide.value && m.belt <= playerBelt.value)
);

const availableCategories = computed(() => 
  [...new Set(availableBattleMoves.value.map(m => m.category))]
);

const categoryMoves = computed(() => 
  availableBattleMoves.value.filter(m => m.category === activeCategory.value)
);

// --- HELPERS ---

const getPositionName = (id) => POSITIONS.find(p => p.id === id)?.name || '';
const getBeltName = (level) => BELTS.find(b => b.level === level)?.name || '';
const getBeltColor = (level) => BELTS.find(b => b.level === level)?.color || '';

// --- TYPEWRITER EFFECT ---

let typewriterInterval;
watch(dialogueText, (newText) => {
  if (!newText) return;
  let i = 0;
  displayedText.value = "";
  clearInterval(typewriterInterval);
  typewriterInterval = setInterval(() => {
    displayedText.value += newText.charAt(i);
    i++;
    if (i >= newText.length) clearInterval(typewriterInterval);
  }, 10);
});

onUnmounted(() => clearInterval(typewriterInterval));

// --- ACTIONS ---

const handleSelectPosition = (id, side) => {
  activePosId.value = id;
  activeSide.value = side;
  scene.value = 'battle';
  menuState.value = 'main';
  activeCategory.value = null;
  activeMove.value = null;
  const posName = getPositionName(id);
  dialogueText.value = `Encounter engaged! Position: ${posName} (${side.toUpperCase()}).\nAwaiting command...`;
};

const handleSelectCategory = (category) => {
  activeCategory.value = category;
  menuState.value = 'category';
  dialogueText.value = `Strategy: ${category}. Select technique.`;
};

const handleSelectMove = (move) => {
  activeMove.value = move;
  menuState.value = 'result';
  let text = `> Executed: ${move.name}\n${move.desc}`;
  if (move.isSub) text += `\n\nCRITICAL! Opponent taps out!`;
  else if (move.transition) text += `\n\nTransition available to ${getPositionName(move.transition.posId)}.`;
  dialogueText.value = text;
};

const executeTransition = () => {
  if (activeMove.value && activeMove.value.transition) {
    handleSelectPosition(activeMove.value.transition.posId, activeMove.value.transition.side);
  } else {
    scene.value = 'map'; // If submission, reset to map
  }
};

const handleBackToMain = () => {
  menuState.value = 'main';
  activeCategory.value = null;
  activeMove.value = null;
  dialogueText.value = `Returning to main menu. Awaiting command...`;
};

const openYoutubeLink = (query) => {
  window.open(`https://www.youtube.com/results?search_query=bjj+${encodeURIComponent(query)}`, '_blank');
};

</script>

<style scoped>
::-webkit-scrollbar { width: 4px; }
::-webkit-scrollbar-track { background: transparent; }
::-webkit-scrollbar-thumb { background: #334155; }
::-webkit-scrollbar-thumb:hover { background: #6366f1; }
</style>