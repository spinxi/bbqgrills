<script setup>
import { Search, ShoppingBag, Flame } from 'lucide-vue-next'

const props = defineProps({
  currentLang: {
    type: String,
    required: true
  },
  t: {
    type: Object,
    required: true
  },
  cartCount: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits([
  'update:currentLang', 
  'open-cart',
  'open-search',
  'filter-category',
  'scroll-to-section'
])

const languages = [
  { code: 'ka', label: 'KA' },
  { code: 'en', label: 'EN' },
  { code: 'ru', label: 'RU' }
]
</script>

<template>
  <header class="fixed top-0 left-0 right-0 z-40 h-16 md:h-20 bg-zinc-950/95 backdrop-blur-md border-b border-zinc-800/80 transition-all duration-300">
    <div class="max-w-7xl mx-auto px-3 sm:px-6 h-full flex items-center justify-between gap-2 sm:gap-4">
      
      <!-- Left: BBQ Grills Georgia Logo & Brand Mark -->
      <div class="flex items-center gap-2 sm:gap-3 shrink-0">
        <a href="#" class="flex items-center gap-2 sm:gap-2.5 group">
          <div class="h-9 w-9 sm:h-11 sm:w-11 rounded-xl bg-gradient-to-br from-orange-600 to-red-700 flex items-center justify-center text-white shadow-lg shadow-orange-950/40 group-hover:scale-105 transition-transform shrink-0">
            <Flame class="w-5 h-5 sm:w-6 sm:h-6 text-white" />
          </div>
          <div>
            <div class="text-sm sm:text-lg md:text-xl font-black tracking-tight text-white group-hover:text-zinc-200 transition-colors flex items-center gap-1 sm:gap-1.5">
              <span>BBQ GRILLS</span>
              <span class="text-orange-500 font-extrabold text-[9px] sm:text-xs px-1 sm:px-1.5 py-0.5 rounded bg-orange-950/60 border border-orange-800/60">GEORGIA</span>
            </div>
            <div class="text-[8px] sm:text-[10px] tracking-wider font-semibold text-zinc-400 uppercase">
              {{ t.officialStoreTag }}
            </div>
          </div>
        </a>
      </div>

      <!-- Center: Wide Search Trigger Button (Desktop/Tablet only) -->
      <div class="flex-1 max-w-xs md:max-w-sm lg:max-w-md hidden md:block">
        <button
          @click="emit('open-search')"
          class="w-full flex items-center justify-between bg-zinc-900/90 hover:bg-zinc-800/90 border border-zinc-800 hover:border-zinc-700 rounded-full px-3.5 sm:px-4 py-2 text-xs text-zinc-400 hover:text-zinc-200 transition-all cursor-pointer group shadow-inner"
        >
          <span class="flex items-center gap-2 truncate">
            <Search class="w-4 h-4 text-zinc-400 group-hover:text-orange-500 transition-colors shrink-0" />
            <span class="truncate">{{ t.searchTrigger }}</span>
          </span>
          <span class="hidden lg:inline-block px-1.5 py-0.5 rounded text-[10px] font-mono bg-zinc-800 text-zinc-400 border border-zinc-700/60">
            ⌘K
          </span>
        </button>
      </div>

      <!-- Right: Clean Nav Links + Lang Switcher + Cart (Zero Mobile Overflow) -->
      <div class="flex items-center gap-2 sm:gap-3 shrink-0">
        
        <!-- Clean Compact Navigation Links (Desktop) -->
        <nav aria-label="Quick Nav" class="hidden lg:flex items-center gap-5 text-xs font-semibold text-zinc-400">
          <button 
            @click="emit('filter-category', 'all')"
            class="hover:text-white transition-colors cursor-pointer"
          >
            {{ t.navGrills }}
          </button>
          <button 
            @click="emit('filter-category', 'commercial')"
            class="hover:text-white transition-colors cursor-pointer"
          >
            {{ t.navRestaurant }}
          </button>
          <button 
            @click="emit('scroll-to-section', 'delivery-section')"
            class="hover:text-white transition-colors cursor-pointer"
          >
            {{ t.navDelivery }}
          </button>
        </nav>

        <!-- Minimal Language Switcher (KA | EN | RU) -->
        <div class="flex items-center gap-1 sm:gap-1.5 text-[11px] sm:text-xs font-semibold text-zinc-500 tracking-wider bg-zinc-900/90 border border-zinc-800 rounded-lg px-1.5 py-1 sm:px-2">
          <template v-for="(lang, idx) in languages" :key="lang.code">
            <button
              @click="emit('update:currentLang', lang.code)"
              class="hover:text-zinc-200 transition-colors cursor-pointer px-1 py-0.5 rounded"
              :class="currentLang === lang.code ? 'text-white font-black bg-zinc-800' : 'text-zinc-400'"
            >
              {{ lang.label }}
            </button>
            <span v-if="idx < languages.length - 1" class="text-zinc-700 select-none">|</span>
          </template>
        </div>

        <!-- Sleek Cart Button (Fits perfectly on mobile & desktop) -->
        <button
          @click="emit('open-cart')"
          class="flex items-center gap-1.5 px-2.5 sm:px-4 py-1.5 sm:py-2 rounded-full bg-orange-600 hover:bg-orange-500 text-white text-xs font-semibold transition-all cursor-pointer shadow-lg shadow-orange-950/50 active:scale-[0.98] shrink-0"
          aria-label="Cart"
        >
          <ShoppingBag class="w-4 h-4 text-white shrink-0" />
          <span class="hidden sm:inline">{{ t.cart }}</span>
          <span class="font-mono font-bold bg-black/25 px-1.5 py-0.2 rounded-full text-[10px] sm:text-xs shrink-0">
            {{ cartCount }}
          </span>
        </button>

      </div>

    </div>
  </header>
</template>
