<script setup>
import { computed } from 'vue'
import { ShoppingBag, Eye, Flame, Check } from 'lucide-vue-next'

const props = defineProps({
  product: {
    type: Object,
    required: true
  },
  currentLang: {
    type: String,
    required: true
  },
  t: {
    type: Object,
    required: true
  },
  isInCart: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits([
  'add-to-cart', 
  'quick-view'
])

const isOutOfStock = computed(() => {
  return props.product.inStock === false
})

const localizedTitle = computed(() => {
  return props.product.title[props.currentLang] || props.product.title.ka
})
</script>

<template>
  <div class="group flex flex-col bg-zinc-900/40 hover:bg-zinc-900/80 border border-zinc-800/80 hover:border-zinc-700 rounded-2xl overflow-hidden transition-all duration-300">
    
    <!-- Product Image Container -->
    <div 
      class="relative aspect-[4/3] overflow-hidden cursor-pointer flex items-center justify-center p-2 bg-zinc-950"
      @click="emit('quick-view', product)"
    >
      <img
        :src="product.image"
        :alt="localizedTitle"
        class="w-full h-full object-cover opacity-90 group-hover:opacity-100 transition-transform duration-500 group-hover:scale-105"
        loading="lazy"
        @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555041469-a586c61ea9bc?auto=format&fit=crop&w=800&q=80'"
      />

      <!-- Stock / Black Friday Badge Top-Left -->
      <div class="absolute top-3 left-3 z-10 flex flex-col gap-1.5 items-start">
        <span 
          v-if="product.isBlackFriday"
          class="inline-flex items-center gap-1 px-2.5 py-1 rounded-full text-[10px] font-black bg-orange-600 text-white shadow-md uppercase tracking-wider"
        >
          <Flame class="w-3 h-3 fill-white" />
          <span>BLACK FRIDAY</span>
        </span>
        <span 
          v-if="!isOutOfStock"
          class="inline-flex items-center px-2 py-0.5 rounded-full text-[10px] font-semibold bg-emerald-950/80 text-emerald-300 border border-emerald-800/60 backdrop-blur-md"
        >
          ● {{ t.inStockBadge }}
        </span>
      </div>

      <!-- Quick View Hover Button -->
      <div class="absolute bottom-3 right-3 opacity-0 group-hover:opacity-100 transition-opacity z-10">
        <button
          @click.stop="emit('quick-view', product)"
          class="p-2 rounded-full bg-zinc-950/90 hover:bg-zinc-900 text-zinc-200 border border-zinc-700 backdrop-blur-md text-xs font-medium cursor-pointer shadow-lg hover:text-white"
          title="Quick View"
        >
          <Eye class="w-4 h-4" />
        </button>
      </div>

      <!-- Sale / Discount Tag if applicable -->
      <div v-if="product.discountPercent" class="absolute top-3 right-3 z-10">
        <span class="inline-flex items-center px-2 py-0.5 rounded text-[11px] font-mono font-bold bg-red-600 text-white shadow-md">
          -{{ product.discountPercent }}%
        </span>
      </div>
    </div>

    <!-- Card Body -->
    <div class="p-4 sm:p-5 flex-1 flex flex-col justify-between space-y-3 sm:space-y-4">
      
      <div class="space-y-2">
        <!-- Model SKU / Brand in Muted Uppercase -->
        <div class="flex items-center justify-between text-[11px] font-bold">
          <span class="text-orange-400 font-mono tracking-wider">{{ product.sku }}</span>
          <span class="text-zinc-500 uppercase">{{ product.categoryLabel[currentLang] || product.categoryLabel.ka }}</span>
        </div>

        <!-- Product Title (Clickable) -->
        <h3 
          @click="emit('quick-view', product)"
          class="text-sm sm:text-base font-bold text-white leading-snug hover:text-orange-400 transition-colors cursor-pointer line-clamp-2"
        >
          {{ localizedTitle }}
        </h3>

        <!-- Clean Technical Spec Pills -->
        <div class="flex items-center gap-1.5 flex-wrap pt-0.5">
          <span 
            v-for="(pill, idx) in product.specPills" 
            :key="idx"
            class="px-2 py-0.5 rounded-md bg-zinc-800/80 text-zinc-300 text-[10px] sm:text-[11px] font-medium border border-zinc-700/50"
          >
            {{ pill }}
          </span>
        </div>
      </div>

      <!-- Pricing & Actions Row -->
      <div class="space-y-3 pt-3 border-t border-zinc-800/80">
        <!-- Price -->
        <div class="flex items-baseline justify-between">
          <div class="flex items-baseline gap-2">
            <span class="text-xl sm:text-2xl font-black text-white font-mono">
              {{ product.price }} ₾
            </span>
            <span 
              v-if="product.oldPrice" 
              class="text-xs text-zinc-500 line-through font-mono"
            >
              {{ product.oldPrice }} ₾
            </span>
          </div>

          <span class="text-[10px] text-zinc-400 font-medium">{{ t.fastDeliveryTag }}</span>
        </div>

        <!-- Clean Action Buttons: Add to Cart & Quick View -->
        <div class="flex items-center gap-2 pt-1">
          <button
            @click="emit('add-to-cart', product)"
            class="flex-1 py-2.5 px-3 rounded-xl text-xs font-bold transition-all cursor-pointer flex items-center justify-center gap-1.5 shadow-md active:scale-[0.98]"
            :class="isInCart
              ? 'bg-zinc-800 text-emerald-400 border border-emerald-500/40'
              : 'bg-orange-600 hover:bg-orange-500 text-white shadow-orange-950/40'"
          >
            <Check v-if="isInCart" class="w-3.5 h-3.5" />
            <ShoppingBag v-else class="w-3.5 h-3.5" />
            <span>{{ isInCart ? t.inCartBadge : t.addToCart }}</span>
          </button>

          <button
            @click="emit('quick-view', product)"
            class="py-2.5 px-3 rounded-xl bg-zinc-900 hover:bg-zinc-800 border border-zinc-800 text-zinc-300 hover:text-white text-xs font-semibold transition-colors cursor-pointer"
          >
            {{ t.quickView }}
          </button>
        </div>

      </div>

    </div>

  </div>
</template>
