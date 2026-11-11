<script setup>
import { Search, ShoppingBag, MessageCircle, Flame } from 'lucide-vue-next'

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
  <header class="fixed top-0 left-0 right-0 z-40 h-20 bg-zinc-950/95 backdrop-blur-md border-b border-zinc-800/80 transition-all duration-300">
    <div class="max-w-7xl mx-auto px-3.5 sm:px-6 h-full flex items-center justify-between gap-2.5 sm:gap-4 lg:gap-6">
      
      <!-- Left: BBQ Grills Georgia Logo & Brand Mark -->
      <div class="flex items-center gap-2 sm:gap-3 shrink-0">
        <a href="#" class="flex items-center gap-2.5 group">
          <div class="h-10 w-10 sm:h-11 sm:w-11 rounded-xl bg-gradient-to-br from-orange-600 to-red-700 flex items-center justify-center text-white shadow-lg shadow-orange-950/40 group-hover:scale-105 transition-transform">
            <Flame class="w-6 h-6 text-white" />
          </div>
          <div>
            <div class="text-base sm:text-xl font-black tracking-tight text-white group-hover:text-zinc-200 transition-colors flex items-center gap-1.5">
              <span>BBQ GRILLS</span>
              <span class="text-orange-500 font-extrabold text-xs sm:text-sm px-1.5 py-0.5 rounded bg-orange-950/60 border border-orange-800/60">GEORGIA</span>
            </div>
            <div class="text-[9px] sm:text-[10px] tracking-wider font-semibold text-zinc-400 uppercase">
              {{ t.officialStoreTag }}
            </div>
          </div>
        </a>
      </div>

      <!-- Center: Wide Search Trigger Button on Tablet/Desktop, Compact Icon on Mobile -->
      <div class="flex-1 max-w-sm lg:max-w-md mx-1 sm:mx-2 hidden sm:block">
        <button
          @click="emit('open-search')"
          class="w-full flex items-center justify-between bg-zinc-900/90 hover:bg-zinc-800/90 border border-zinc-800 hover:border-zinc-700 rounded-full px-3.5 sm:px-4 py-2 text-xs sm:text-sm text-zinc-400 hover:text-zinc-200 transition-all cursor-pointer group shadow-inner"
        >
          <span class="flex items-center gap-2.5 truncate">
            <Search class="w-4 h-4 text-zinc-400 group-hover:text-orange-500 transition-colors shrink-0" />
            <span class="truncate">{{ t.searchTrigger }}</span>
          </span>
          <span class="hidden md:inline-block px-1.5 py-0.5 rounded text-[10px] font-mono bg-zinc-800 text-zinc-400 border border-zinc-700/60">
            ⌘K
          </span>
        </button>
      </div>

      <!-- Right: WhatsApp + Nav Links + Lang Switcher + Cart -->
      <div class="flex items-center gap-2 sm:gap-3 md:gap-4 shrink-0">
        
        <!-- Mobile Search Icon Button (visible only < 640px) -->
        <button
          @click="emit('open-search')"
          class="sm:hidden p-2 rounded-full bg-zinc-900 hover:bg-zinc-800 text-zinc-400 hover:text-white border border-zinc-800 cursor-pointer"
          aria-label="Search"
        >
          <Search class="w-4 h-4" />
        </button>

        <!-- WhatsApp Quick Button -->
        <a 
          href="https://wa.me/c/995511195755" 
          target="_blank" 
          rel="noopener noreferrer"
          class="hidden lg:flex items-center gap-1.5 px-3 py-1.5 rounded-full bg-emerald-600/15 hover:bg-emerald-600/25 border border-emerald-500/30 text-emerald-400 hover:text-emerald-300 text-xs font-semibold transition-all shadow-sm"
        >
          <MessageCircle class="w-3.5 h-3.5 text-emerald-400 fill-emerald-400/20" />
          <span>{{ t.whatsappCatalogBtn }}</span>
        </a>

        <!-- Clean Navigation Links (Desktop) -->
        <nav aria-label="Quick Nav" class="hidden xl:flex items-center gap-5 text-xs font-semibold text-zinc-400">
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
            @click="emit('scroll-to-section', 'how-to-order')"
            class="hover:text-white transition-colors cursor-pointer"
          >
            {{ t.navHowToOrder }}
          </button>
        </nav>

        <!-- Minimal Text-Only Language Switcher (KA | EN | RU) -->
        <div class="flex items-center gap-1 sm:gap-1.5 text-xs font-semibold text-zinc-500 tracking-wider">
          <template v-for="(lang, idx) in languages" :key="lang.code">
            <button
              @click="emit('update:currentLang', lang.code)"
              class="hover:text-zinc-300 transition-colors cursor-pointer py-1 px-0.5"
              :class="currentLang === lang.code ? 'text-white font-bold' : 'text-zinc-500'"
            >
              {{ lang.label }}
            </button>
            <span v-if="idx < languages.length - 1" class="text-zinc-700">|</span>
          </template>
        </div>

        <!-- Sleek Cart Button -->
        <button
          @click="emit('open-cart')"
          class="flex items-center gap-1.5 sm:gap-2 px-3 sm:px-4 py-2 rounded-full bg-orange-600 hover:bg-orange-500 text-white text-xs font-semibold transition-all cursor-pointer shadow-lg shadow-orange-950/50 active:scale-[0.98]"
        >
          <ShoppingBag class="w-4 h-4 text-white" />
          <span class="hidden sm:inline">{{ t.cart }}</span>
          <span class="font-mono font-bold bg-black/20 px-1.5 py-0.2 rounded-full">({{ cartCount }})</span>
        </button>

      </div>

    </div>
  </header>
</template>
