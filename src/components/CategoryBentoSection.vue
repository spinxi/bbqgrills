<script setup>
import { computed } from 'vue'

const props = defineProps({
  selectedCategory: {
    type: String,
    default: 'all'
  },
  productsCountByCategory: {
    type: Object,
    default: () => ({})
  },
  t: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['select-category'])

const categories = computed(() => [
  {
    id: 'all',
    title: props.t.catAll,
    image: 'https://images.unsplash.com/photo-1555041469-a586c61ea9bc?auto=format&fit=crop&w=600&q=80',
    countKey: 'all'
  },
  {
    id: 'commercial',
    title: props.t.catCommercial,
    image: 'https://images.unsplash.com/photo-1544025162-d76694265947?auto=format&fit=crop&w=600&q=80',
    countKey: 'commercial'
  },
  {
    id: 'charcoal-ceramic',
    title: props.t.catCharcoalCeramic,
    image: 'https://images.unsplash.com/photo-1529193591184-b1d58069ecdd?auto=format&fit=crop&w=600&q=80',
    countKey: 'charcoal-ceramic'
  },
  {
    id: 'accessories',
    title: props.t.catAccessories,
    image: 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?auto=format&fit=crop&w=600&q=80',
    countKey: 'accessories'
  }
])
</script>

<template>
  <section class="py-8 sm:py-10 bg-zinc-950 border-b border-zinc-900">
    <div class="max-w-7xl mx-auto px-4 sm:px-6">
      
      <!-- Section Title -->
      <div class="flex items-center justify-between mb-4 sm:mb-6">
        <div>
          <h2 class="text-lg sm:text-2xl font-bold text-white tracking-tight">
            {{ t.categoriesTitle }}
          </h2>
          <p class="text-xs text-zinc-400 mt-0.5">
            {{ t.categoriesSubtitle }}
          </p>
        </div>
      </div>

      <!-- 4-Card Category Bento Grid -->
      <div class="grid grid-cols-2 lg:grid-cols-4 gap-3 sm:gap-5">
        <div
          v-for="cat in categories"
          :key="cat.id"
          @click="emit('select-category', cat.id)"
          class="h-32 sm:h-40 relative overflow-hidden rounded-2xl border group cursor-pointer transition-all duration-300"
          :class="selectedCategory === cat.id 
            ? 'border-orange-500 ring-2 ring-orange-500/50 shadow-xl shadow-orange-950/40' 
            : 'border-zinc-800 hover:border-zinc-600'"
        >
          <!-- Background Image with Dark Overlay -->
          <img
            :src="cat.image"
            :alt="cat.title"
            class="w-full h-full object-cover object-center group-hover:scale-105 transition-transform duration-500 opacity-55 group-hover:opacity-70"
            @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?auto=format&fit=crop&w=600&q=80'"
          />
          <div class="absolute inset-0 bg-gradient-to-t from-zinc-950 via-zinc-950/60 to-transparent"></div>

          <!-- Item Count Badge Top-Right -->
          <div class="absolute top-3 right-3 z-10">
            <span class="px-2.5 py-0.5 rounded-full text-[10px] font-mono font-bold bg-zinc-950/80 text-zinc-300 border border-zinc-700/80 backdrop-blur-sm">
              {{ productsCountByCategory[cat.countKey] || 0 }}
            </span>
          </div>

          <!-- Title and Icon Bottom-Left -->
          <div class="absolute bottom-3 left-3 right-3 z-10">
            <h3 class="text-xs sm:text-sm font-bold text-white group-hover:text-orange-400 transition-colors line-clamp-2 leading-snug">
              {{ cat.title }}
            </h3>
          </div>
        </div>
      </div>

    </div>
  </section>
</template>
