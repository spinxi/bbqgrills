<script setup>
import { computed } from 'vue'
import { ShoppingBag, Bell, Eye } from 'lucide-vue-next'

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
  'open-sms', 
  'quick-view'
])

// Total stock across branches
const totalStock = computed(() => {
  return (props.product.stockBeliashvili || 0) + (props.product.stockCityMall || 0)
})

const isOutOfStock = computed(() => {
  return totalStock.value <= 0
})

const localizedTitle = computed(() => {
  return props.product.title[props.currentLang] || props.product.title.ka
})
</script>

<template>
  <div class="group flex flex-col bg-zinc-900/40 hover:bg-zinc-900/70 border border-zinc-800/80 hover:border-zinc-700 rounded-2xl overflow-hidden transition-all duration-300">
    
    <!-- Product Image Container (Apple Store / Weber Studio Style) -->
    <div 
      class="relative aspect-[4/3] overflow-hidden cursor-pointer flex items-center justify-center p-2"
      :class="(product.isPNGStudio || (product.image && product.image.includes('grills.ge')))
        ? 'bg-gradient-to-b from-zinc-800/60 to-zinc-900/90' 
        : 'bg-zinc-950'"
      @click="emit('quick-view', product)"
    >
      <img
        :src="product.image"
        :alt="localizedTitle"
        class="transition-transform duration-500 group-hover:scale-105"
        :class="(product.isPNGStudio || (product.image && product.image.includes('grills.ge')))
          ? 'w-full h-full object-contain p-4' 
          : 'w-full h-full object-cover opacity-90 group-hover:opacity-100'"
        loading="lazy"
        @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?auto=format&fit=crop&w=800&q=80'"
      />

      <!-- Subtle Stock Badge Top-Left -->
      <div class="absolute top-3.5 left-3.5 z-10">
        <span 
          v-if="!isOutOfStock"
          class="inline-flex items-center px-2.5 py-1 rounded-full text-[11px] font-medium bg-emerald-950/80 text-emerald-300 border border-emerald-800/60 backdrop-blur-md"
        >
          {{ t.inStockBadge }}
        </span>
        <span 
          v-else
          class="inline-flex items-center px-2.5 py-1 rounded-full text-[11px] font-medium bg-zinc-900/90 text-zinc-400 border border-zinc-700/80 backdrop-blur-md"
        >
          {{ t.outOfStockBadge }}
        </span>
      </div>

      <!-- Quick View Hover Button -->
      <div class="absolute bottom-3 right-3 opacity-0 group-hover:opacity-100 transition-opacity z-10">
        <button
          @click.stop="emit('quick-view', product)"
          class="p-2 rounded-full bg-zinc-950/80 hover:bg-zinc-900 text-zinc-200 border border-zinc-700 backdrop-blur-md text-xs font-medium cursor-pointer shadow-lg hover:text-white"
          title="Quick View"
        >
          <Eye class="w-4 h-4" />
        </button>
      </div>

      <!-- Sale / Discount Tag if applicable -->
      <div v-if="product.discountPercent" class="absolute top-3.5 right-3.5 z-10">
        <span class="inline-flex items-center px-2 py-0.5 rounded text-[11px] font-bold bg-red-600/90 text-white border border-red-500 shadow-sm">
          -{{ product.discountPercent }}%
        </span>
      </div>
    </div>

    <!-- Editorial Card Body -->
    <div class="p-4 sm:p-6 flex-1 flex flex-col justify-between space-y-3 sm:space-y-4">
      
      <div class="space-y-2">
        <!-- Brand Name in Small Muted Uppercase -->
        <div class="text-[11px] font-bold uppercase tracking-wider text-zinc-500">
          {{ product.brand }}
        </div>

        <!-- Clean Product Title (Clickable) -->
        <h3 
          @click="emit('quick-view', product)"
          class="text-base font-semibold text-white leading-snug hover:text-zinc-300 transition-colors cursor-pointer line-clamp-2"
        >
          {{ localizedTitle }}
        </h3>

        <!-- 2-3 Clean Technical Spec Pills Below Title -->
        <div class="flex items-center gap-1.5 flex-wrap pt-1">
          <span 
            v-for="(pill, idx) in product.specPills" 
            :key="idx"
            class="px-2 py-0.5 rounded bg-zinc-800/80 text-zinc-300 text-[11px] font-medium border border-zinc-700/50"
          >
            {{ pill }}
          </span>
        </div>

        <!-- Subtle Branch Availability Text -->
        <div class="text-xs text-zinc-400 font-normal pt-1">
          <span>{{ t.branchBeliashviliName }} <strong :class="product.stockBeliashvili > 0 ? 'text-zinc-200' : 'text-zinc-600'">{{ product.stockBeliashvili }}</strong></span>
          <span class="mx-1.5 text-zinc-600">•</span>
          <span>{{ t.branchCityMallName }} <strong :class="product.stockCityMall > 0 ? 'text-zinc-200' : 'text-zinc-600'">{{ product.stockCityMall }}</strong></span>
        </div>
      </div>

      <!-- Pricing & Action Button -->
      <div class="space-y-3 pt-3 border-t border-zinc-800/80">
        <!-- Price -->
        <div class="flex items-baseline gap-2.5">
          <span class="text-xl sm:text-2xl font-bold text-white font-mono">
            {{ product.price.toLocaleString() }} ₾
          </span>
          <span 
            v-if="product.oldPrice" 
            class="text-xs text-zinc-500 line-through font-mono"
          >
            {{ product.oldPrice.toLocaleString() }} ₾
          </span>
        </div>

        <!-- Action Button -->
        <div class="pt-1">
          <button
            v-if="!isOutOfStock"
            @click="emit('add-to-cart', product)"
            class="w-full py-2.5 px-4 rounded-xl text-xs font-semibold transition-all cursor-pointer flex items-center justify-center gap-2"
            :class="isInCart
              ? 'bg-zinc-800 text-zinc-200 hover:bg-zinc-700'
              : 'bg-zinc-100 hover:bg-white text-zinc-950 font-bold'"
          >
            <ShoppingBag class="w-3.5 h-3.5" />
            <span>{{ isInCart ? t.inCartBadge : t.addToCart }}</span>
          </button>

          <button
            v-else
            @click="emit('open-sms', product)"
            class="w-full py-2.5 px-4 rounded-xl bg-zinc-900 hover:bg-zinc-800 border border-zinc-700 text-zinc-300 hover:text-white text-xs font-semibold transition-all cursor-pointer flex items-center justify-center gap-2"
          >
            <Bell class="w-3.5 h-3.5 text-zinc-400" />
            <span>{{ t.notifySms }}</span>
          </button>
        </div>

      </div>

    </div>

  </div>
</template>
