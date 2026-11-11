<script setup>
import { ref, computed, watch, nextTick } from 'vue'
import { Search, X, ArrowRight } from 'lucide-vue-next'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },
  products: {
    type: Array,
    required: true
  },
  currentLang: {
    type: String,
    required: true
  },
  t: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['close', 'select-product'])

const searchQuery = ref('')
const searchInput = ref(null)

const popularSearches = computed(() => {
  return ['#5 Model XL (72x42)', '#7 Starter 555', '#3 Model 459', '#2 Model 179', '#8 Starter 59', 'D59', 'D54']
})

watch(() => props.isOpen, (newVal) => {
  if (newVal) {
    searchQuery.value = ''
    nextTick(() => {
      searchInput.value?.focus()
    })
  }
})

const matchingProducts = computed(() => {
  if (!searchQuery.value.trim()) return []
  const q = searchQuery.value.toLowerCase().trim()
  return props.products.filter(p => {
    const title = (p.title[props.currentLang] || p.title.ka || '').toLowerCase()
    const sku = (p.sku || '').toLowerCase()
    const specs = Object.values(p.specs || {}).join(' ').toLowerCase()
    return title.includes(q) || sku.includes(q) || specs.includes(q)
  })
})

const setPopularQuery = (query) => {
  searchQuery.value = query
  searchInput.value?.focus()
}

const selectAndClose = (product) => {
  emit('select-product', product)
  emit('close')
}
</script>

<template>
  <div 
    v-if="isOpen"
    class="fixed inset-0 z-50 bg-black/85 backdrop-blur-md flex items-start justify-center p-4 sm:p-6 pt-16 sm:pt-24 animate-in fade-in duration-200"
    @click.self="emit('close')"
    @keydown.esc="emit('close')"
  >
    <div class="relative w-full max-w-2xl bg-zinc-900 border border-zinc-700/80 rounded-2xl shadow-2xl overflow-hidden flex flex-col max-h-[80vh]">
      
      <!-- Top Search Input Bar -->
      <div class="p-4 sm:p-5 border-b border-zinc-800 flex items-center gap-3 bg-zinc-950">
        <Search class="w-5 h-5 text-orange-500 shrink-0" />
        <input 
          ref="searchInput"
          type="text"
          v-model="searchQuery"
          :placeholder="t.searchModalPlaceholder"
          class="w-full bg-transparent text-sm sm:text-base text-white placeholder-zinc-500 outline-none"
        />
        <button
          v-if="searchQuery"
          @click="searchQuery = ''"
          class="p-1 text-zinc-500 hover:text-zinc-300 rounded cursor-pointer"
        >
          <X class="w-4 h-4" />
        </button>
        <button 
          @click="emit('close')"
          class="px-2 py-1 text-xs text-zinc-400 hover:text-white rounded bg-zinc-800/80 cursor-pointer"
        >
          ESC
        </button>
      </div>

      <!-- Popular Searches Strip -->
      <div class="px-5 py-3 bg-zinc-950/60 border-b border-zinc-800/60 flex items-center gap-2 overflow-x-auto text-xs scrollbar-none">
        <span class="text-zinc-500 shrink-0 font-medium">{{ t.popularSearchesLabel }}</span>
        <button
          v-for="tag in popularSearches"
          :key="tag"
          @click="setPopularQuery(tag)"
          class="px-2.5 py-1 rounded-full bg-zinc-800 hover:bg-zinc-700 text-zinc-300 hover:text-white transition-colors shrink-0 cursor-pointer text-[11px]"
        >
          {{ tag }}
        </button>
      </div>

      <!-- Results List -->
      <div class="flex-1 overflow-y-auto p-4 space-y-2">
        <!-- If query is typed and results found -->
        <template v-if="matchingProducts.length > 0">
          <div 
            v-for="product in matchingProducts"
            :key="product.id"
            @click="selectAndClose(product)"
            class="p-3 rounded-xl bg-zinc-950/60 hover:bg-zinc-800/80 border border-zinc-800/80 hover:border-zinc-700 flex items-center justify-between gap-3 cursor-pointer transition-all group"
          >
            <div class="flex items-center gap-3 min-w-0">
              <img 
                :src="product.image" 
                :alt="product.title.ka" 
                class="w-12 h-12 rounded-lg object-cover border border-zinc-800 shrink-0"
                @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555041469-a586c61ea9bc?auto=format&fit=crop&w=800&q=80'"
              />
              <div class="min-w-0">
                <div class="text-[10px] font-bold text-orange-400 font-mono tracking-wider">
                  {{ product.sku }}
                </div>
                <div class="text-xs sm:text-sm font-semibold text-white truncate group-hover:text-orange-400 transition-colors">
                  {{ product.title[currentLang] || product.title.ka }}
                </div>
                <div class="text-[11px] text-zinc-400 mt-0.5">
                  <span class="text-emerald-400">
                    ● {{ t.inStockBadge }}
                  </span>
                </div>
              </div>
            </div>

            <div class="text-right shrink-0">
              <div class="text-xs sm:text-sm font-bold text-white font-mono">
                {{ product.price }} ₾
              </div>
              <div class="text-[10px] text-zinc-500 group-hover:text-orange-400 flex items-center gap-1 justify-end mt-0.5">
                <span>{{ t.viewProduct }}</span>
                <ArrowRight class="w-3 h-3" />
              </div>
            </div>
          </div>
        </template>

        <!-- No results -->
        <div v-else-if="searchQuery" class="p-8 text-center text-zinc-500 space-y-1">
          <div class="text-sm font-medium text-zinc-400">{{ t.searchNoResults }}</div>
          <div class="text-xs">{{ t.searchTryDifferent }}</div>
        </div>

        <!-- Initial State (Before typing) -->
        <div v-else class="p-8 text-center text-zinc-500 space-y-2">
          <Search class="w-8 h-8 mx-auto text-zinc-700" />
          <div class="text-xs text-zinc-400">{{ t.searchInitialPrompt }}</div>
        </div>
      </div>

      <!-- Footer Info -->
      <div class="px-5 py-2.5 bg-zinc-950 border-t border-zinc-800 text-[11px] text-zinc-500 flex items-center justify-between">
        <span>{{ t.searchFooterTag }}</span>
        <span>{{ t.searchEscClose }}</span>
      </div>

    </div>
  </div>
</template>
