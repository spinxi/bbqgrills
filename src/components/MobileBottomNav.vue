<script setup>
import { ShoppingBag, Search, Store, Grid, Phone } from 'lucide-vue-next'

const props = defineProps({
  cartCount: {
    type: Number,
    default: 0
  },
  t: {
    type: Object,
    required: true
  }
})

const emit = defineEmits([
  'open-cart', 
  'open-search', 
  'scroll-to-catalog', 
  'scroll-to-showrooms'
])
</script>

<template>
  <nav 
    aria-label="Mobile Navigation"
    class="md:hidden fixed bottom-0 left-0 right-0 z-40 bg-zinc-950/95 backdrop-blur-xl border-t border-zinc-800/90 px-3 py-2 flex items-center justify-around shadow-2xl safe-area-inset-bottom"
  >
    <!-- Catalog -->
    <button
      @click="emit('scroll-to-catalog')"
      class="flex flex-col items-center justify-center gap-1 text-zinc-400 hover:text-white transition-colors cursor-pointer py-1 px-2 group min-w-[56px]"
    >
      <Grid class="w-5 h-5 group-hover:text-red-500 transition-colors" />
      <span class="text-[10px] font-medium tracking-tight truncate max-w-[64px]">
        {{ t.navGrills }}
      </span>
    </button>

    <!-- Search -->
    <button
      @click="emit('open-search')"
      class="flex flex-col items-center justify-center gap-1 text-zinc-400 hover:text-white transition-colors cursor-pointer py-1 px-2 group min-w-[56px]"
    >
      <Search class="w-5 h-5 group-hover:text-red-500 transition-colors" />
      <span class="text-[10px] font-medium tracking-tight truncate max-w-[64px]">
        {{ t.searchPlaceholder.split('...')[0].replace('Поиск', 'Поиск').replace('Search', 'Search') || 'Search' }}
      </span>
    </button>

    <!-- Showrooms -->
    <button
      @click="emit('scroll-to-showrooms')"
      class="flex flex-col items-center justify-center gap-1 text-zinc-400 hover:text-white transition-colors cursor-pointer py-1 px-2 group min-w-[56px]"
    >
      <Store class="w-5 h-5 group-hover:text-red-500 transition-colors" />
      <span class="text-[10px] font-medium tracking-tight truncate max-w-[64px]">
        {{ t.navShowrooms }}
      </span>
    </button>

    <!-- Call Showroom -->
    <a
      href="tel:+995322123456"
      class="flex flex-col items-center justify-center gap-1 text-zinc-400 hover:text-white transition-colors cursor-pointer py-1 px-2 group min-w-[56px]"
    >
      <Phone class="w-5 h-5 group-hover:text-red-500 transition-colors" />
      <span class="text-[10px] font-medium tracking-tight truncate max-w-[64px]">
        {{ t.footerContact }}
      </span>
    </a>

    <!-- Cart Button with Live Badge -->
    <button
      @click="emit('open-cart')"
      class="relative flex flex-col items-center justify-center gap-1 text-zinc-400 hover:text-white transition-colors cursor-pointer py-1 px-2 group min-w-[56px]"
    >
      <div class="relative">
        <ShoppingBag class="w-5 h-5 group-hover:text-red-500 transition-colors" />
        <span 
          v-if="cartCount > 0"
          class="absolute -top-1.5 -right-2 w-4 h-4 rounded-full bg-red-600 text-white text-[10px] font-black flex items-center justify-center shadow-sm"
        >
          {{ cartCount }}
        </span>
      </div>
      <span class="text-[10px] font-medium tracking-tight truncate max-w-[64px]" :class="{ 'text-red-500 font-bold': cartCount > 0 }">
        {{ t.cart }}
      </span>
    </button>
  </nav>
</template>
