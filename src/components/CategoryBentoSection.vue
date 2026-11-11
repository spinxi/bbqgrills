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
    image: 'https://images.unsplash.com/photo-1555396273-367ea4eb4db5?auto=format&fit=crop&w=600&q=80',
    countKey: 'all'
  },
  {
    id: 'charcoal-ceramic',
    title: props.t.catCharcoalCeramic,
    image: 'https://images.unsplash.com/photo-1544025162-d76694265947?auto=format&fit=crop&w=600&q=80',
    countKey: 'charcoal-ceramic'
  },
  {
    id: 'gas',
    title: props.t.catGas,
    image: 'https://images.unsplash.com/photo-1527477378408-1bc09c23310a?auto=format&fit=crop&w=600&q=80',
    countKey: 'gas'
  },
  {
    id: 'pizza',
    title: props.t.catPizza,
    image: 'https://images.unsplash.com/photo-1513104890138-7c749659a591?auto=format&fit=crop&w=600&q=80',
    countKey: 'pizza'
  },
  {
    id: 'cutlery',
    title: props.t.catCutlery,
    image: 'https://images.unsplash.com/photo-1593618998160-e34014e67546?auto=format&fit=crop&w=600&q=80',
    countKey: 'cutlery'
  },
  {
    id: 'accessories',
    title: props.t.catAccessories,
    image: 'https://images.unsplash.com/photo-1584269600464-37b1b58a9fe7?auto=format&fit=crop&w=600&q=80',
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

      <!-- 6-Card Category Bento Grid -->
      <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-6 gap-2.5 sm:gap-4">
        <div
          v-for="cat in categories"
          :key="cat.id"
          @click="emit('select-category', cat.id)"
          class="h-28 sm:h-36 relative overflow-hidden rounded-2xl border group cursor-pointer transition-all duration-300"
          :class="selectedCategory === cat.id 
            ? 'border-red-600 ring-1 ring-red-600 shadow-lg shadow-red-950/40' 
            : 'border-zinc-800 hover:border-zinc-600'"
        >
          <!-- Background Image with Dark Overlay -->
          <img
            :src="cat.image"
            :alt="cat.title"
            class="w-full h-full object-cover object-center group-hover:scale-105 transition-transform duration-500 opacity-60 group-hover:opacity-75"
            @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?auto=format&fit=crop&w=600&q=80'"
          />
          <div class="absolute inset-0 bg-gradient-to-t from-zinc-950 via-zinc-950/60 to-transparent"></div>

          <!-- Item Count Badge Top-Right -->
          <div class="absolute top-2.5 right-2.5 z-10">
            <span class="px-2 py-0.5 rounded-full text-[10px] font-mono font-bold bg-zinc-950/80 text-zinc-300 border border-zinc-700/80 backdrop-blur-sm">
              {{ productsCountByCategory[cat.countKey] || 0 }}
            </span>
          </div>

          <!-- Category Title Bottom-Left -->
          <div class="absolute bottom-3 left-3 right-3 z-10">
            <div 
              class="text-xs sm:text-sm font-bold text-white leading-snug group-hover:text-red-400 transition-colors drop-shadow"
              :class="{ 'text-red-400': selectedCategory === cat.id }"
            >
              {{ cat.title }}
            </div>
          </div>
        </div>
      </div>

    </div>
  </section>
</template>
