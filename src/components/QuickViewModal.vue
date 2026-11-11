<script setup>
import { ref, computed, watch } from 'vue'
import { 
  X, 
  ShoppingBag, 
  Truck, 
  ShieldCheck, 
  Plus, 
  Minus, 
  Flame,
  CheckCircle2
} from 'lucide-vue-next'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },
  product: {
    type: Object,
    default: null
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

const emit = defineEmits(['close', 'add-to-cart'])

const activeImageIndex = ref(0)
const quantity = ref(1)

watch(() => props.product, () => {
  activeImageIndex.value = 0
  quantity.value = 1
})

const currentImage = computed(() => {
  if (!props.product) return ''
  if (props.product.gallery && props.product.gallery[activeImageIndex.value]) {
    return props.product.gallery[activeImageIndex.value]
  }
  return props.product.image
})

const localizedTitle = computed(() => {
  if (!props.product) return ''
  return props.product.title[props.currentLang] || props.product.title.ka
})

const localizedSubtitle = computed(() => {
  if (!props.product) return ''
  return props.product.subtitle[props.currentLang] || props.product.subtitle.ka
})

const localizedCategory = computed(() => {
  if (!props.product) return ''
  if (props.product.categoryLabel) {
    return props.product.categoryLabel[props.currentLang] || props.product.categoryLabel.ka
  }
  return 'გრილი'
})

const handleAddToCart = () => {
  emit('add-to-cart', props.product, quantity.value)
  emit('close')
}

const decrementQuantity = () => {
  if (quantity.value > 1) {
    quantity.value--
  }
}

const incrementQuantity = () => {
  quantity.value++
}
</script>

<template>
  <div 
    v-if="isOpen && product"
    class="fixed inset-0 z-50 overflow-y-auto bg-black/85 backdrop-blur-md flex items-center justify-center p-2.5 sm:p-6"
    @click.self="emit('close')"
  >
    <div class="relative w-full max-w-4xl bg-zinc-900 border border-zinc-800 rounded-3xl shadow-2xl overflow-hidden my-auto max-h-[92vh] flex flex-col overflow-y-auto animate-in fade-in zoom-in-95 duration-200">
      
      <!-- Close Button -->
      <button 
        @click="emit('close')"
        class="absolute top-3 right-3 sm:top-4 sm:right-4 z-30 p-2 sm:p-2.5 rounded-full bg-zinc-950/90 hover:bg-zinc-800 text-zinc-400 hover:text-white transition-colors cursor-pointer border border-zinc-800 shadow-md"
        aria-label="Close"
      >
        <X class="w-4 h-4 sm:w-5 sm:h-5" />
      </button>

      <!-- Modal Content Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2">
        
        <!-- Left: Image Gallery & Thumbnails Switcher -->
        <div class="p-4 sm:p-8 bg-zinc-950 flex flex-col justify-between border-b md:border-b-0 md:border-r border-zinc-800/80">
          <div class="space-y-4">
            <!-- Large Image Preview -->
            <div class="relative w-full aspect-square rounded-2xl overflow-hidden border border-zinc-800 shadow-inner flex items-center justify-center p-3 bg-zinc-900">
              <img 
                :src="currentImage" 
                :alt="localizedTitle" 
                class="w-full h-full object-cover transition-all duration-300"
                @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555041469-a586c61ea9bc?auto=format&fit=crop&w=800&q=80'"
              />
              <div class="absolute top-3.5 left-3.5 z-10 flex flex-col gap-1.5 items-start">
                <span class="px-2.5 py-1 rounded-lg text-xs font-bold uppercase tracking-wider bg-zinc-950/85 text-orange-400 border border-zinc-700/80 backdrop-blur-sm">
                  {{ product.sku }}
                </span>
                <span v-if="product.isBlackFriday" class="px-2.5 py-0.5 rounded-full text-[10px] font-black uppercase tracking-wider bg-orange-600 text-white shadow-md flex items-center gap-1">
                  <Flame class="w-3 h-3 fill-white" />
                  <span>BLACK FRIDAY</span>
                </span>
              </div>
            </div>

            <!-- Thumbnails Switcher Below Main Image -->
            <div class="flex items-center gap-3">
              <button
                v-for="(img, idx) in (product.gallery || [product.image])"
                :key="idx"
                @click="activeImageIndex = idx"
                class="w-16 h-16 sm:w-20 sm:h-20 rounded-xl overflow-hidden border-2 transition-all cursor-pointer bg-zinc-900 flex items-center justify-center p-1"
                :class="activeImageIndex === idx 
                  ? 'border-orange-500 scale-105 shadow-md shadow-orange-950/40' 
                  : 'border-zinc-800 hover:border-zinc-600 opacity-60 hover:opacity-100'"
              >
                <img 
                  :src="img" 
                  alt="Product view" 
                  class="w-full h-full object-cover rounded-lg"
                  @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555041469-a586c61ea9bc?auto=format&fit=crop&w=800&q=80'"
                />
              </button>
            </div>
          </div>

          <!-- Quality Guarantee Note -->
          <div class="mt-4 p-3 rounded-xl bg-zinc-900/60 border border-zinc-800 text-xs text-zinc-400 flex items-center gap-2.5">
            <ShieldCheck class="w-4 h-4 text-emerald-400 shrink-0" />
            <span>{{ t.onlineShopGuarantee }}</span>
          </div>
        </div>

        <!-- Right: Details, Stock Status, Specs Table & Actions -->
        <div class="p-4 sm:p-8 flex flex-col justify-between space-y-5 sm:space-y-6">
          
          <div class="space-y-4">
            
            <!-- Category & SKU Code -->
            <div class="flex items-center justify-between text-[11px] text-zinc-500 font-mono pr-12">
              <div class="flex items-center gap-2">
                <span class="font-bold text-orange-400 uppercase tracking-wider">BBQ GRILLS GEORGIA</span>
                <span>•</span>
                <span class="text-zinc-300 font-semibold">{{ localizedCategory }}</span>
              </div>
            </div>

            <!-- Title & Subtitle -->
            <div>
              <h2 class="text-xl sm:text-2xl font-black text-white leading-snug">
                {{ localizedTitle }}
              </h2>
              <p class="text-xs text-zinc-400 mt-1 leading-relaxed">
                {{ localizedSubtitle }}
              </p>
            </div>

            <!-- Price -->
            <div class="flex items-baseline gap-3 pb-3 border-b border-zinc-800">
              <span class="text-2xl sm:text-3xl font-black text-white font-mono">
                {{ product.price }} ₾
              </span>
              <span 
                v-if="product.oldPrice" 
                class="text-sm text-zinc-500 line-through font-mono"
              >
                {{ product.oldPrice }} ₾
              </span>
              <span v-if="product.discountPercent" class="text-xs font-bold text-orange-400 bg-orange-950/60 px-2 py-0.5 rounded border border-orange-800/60">
                {{ t.discountPrefix }} -{{ product.discountPercent }}%
              </span>
            </div>

            <!-- Online Stock & Delivery Status -->
            <div class="space-y-2">
              <div class="flex items-center justify-between px-3.5 py-2.5 bg-zinc-950/90 rounded-xl border border-zinc-800 text-xs">
                <div class="flex items-center gap-2">
                  <span class="w-2.5 h-2.5 rounded-full bg-emerald-400 animate-pulse"></span>
                  <span class="text-emerald-300 font-bold">{{ t.warehouseStockLive }}</span>
                </div>
                <div class="flex items-center gap-1.5 text-zinc-400">
                  <Truck class="w-3.5 h-3.5 text-orange-400" />
                  <span>{{ t.deliveryPricingNote }}</span>
                </div>
              </div>
            </div>

            <!-- Technical Specifications Table -->
            <div class="space-y-2 pt-1">
              <div class="text-xs font-semibold text-zinc-300">
                {{ t.techSpecs }}
              </div>
              <div class="rounded-xl border border-zinc-800 bg-zinc-950/60 overflow-hidden divide-y divide-zinc-800/80 text-xs">
                <div v-if="product.specs.cookingArea" class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specDiameter }}</span>
                  <span class="font-bold text-white font-mono">{{ product.specs.cookingArea }}</span>
                </div>
                <div v-if="product.specs.capacity" class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specCapacity }}</span>
                  <span class="font-bold text-white">{{ product.specs.capacity }}</span>
                </div>
                <div v-if="product.specs.weight" class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specWeight }}</span>
                  <span class="font-bold text-white font-mono">{{ product.specs.weight }}</span>
                </div>
                <div v-if="product.specs.fuel" class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specFuel }}</span>
                  <span class="font-medium text-zinc-300">{{ product.specs.fuel }}</span>
                </div>
                <div v-if="product.specs.material" class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specMaterial }}</span>
                  <span class="font-medium text-zinc-300">{{ product.specs.material }}</span>
                </div>
              </div>
            </div>

            <!-- Features list -->
            <div v-if="product.features && (product.features[currentLang] || product.features.ka)" class="space-y-1.5 pt-1">
              <div 
                v-for="(feat, idx) in (product.features[currentLang] || product.features.ka).slice(0, 3)" 
                :key="idx"
                class="flex items-start gap-2 text-[11px] text-zinc-400 leading-snug"
              >
                <CheckCircle2 class="w-3.5 h-3.5 text-orange-400 shrink-0 mt-0.5" />
                <span>{{ feat }}</span>
              </div>
            </div>

          </div>

          <!-- Bottom Action: Quantity Selector + Add to Cart -->
          <div class="space-y-2.5 pt-3 border-t border-zinc-800">
            <div class="flex items-center gap-2.5">
              <!-- Quantity Counter -->
              <div class="flex items-center border border-zinc-700 bg-zinc-950 rounded-xl px-2 py-1 shrink-0">
                <button 
                  @click="decrementQuantity"
                  class="p-1.5 text-zinc-400 hover:text-white cursor-pointer"
                  title="Decrease"
                >
                  <Minus class="w-3.5 h-3.5" />
                </button>
                <span class="px-3 text-sm font-bold font-mono text-white">{{ quantity }}</span>
                <button 
                  @click="incrementQuantity"
                  class="p-1.5 text-zinc-400 hover:text-white cursor-pointer"
                  title="Increase"
                >
                  <Plus class="w-3.5 h-3.5" />
                </button>
              </div>

              <!-- Add to Cart Button -->
              <button
                @click="handleAddToCart"
                class="flex-1 py-3.5 px-5 rounded-xl bg-orange-600 hover:bg-orange-500 text-white font-bold text-xs sm:text-sm flex items-center justify-center gap-2 cursor-pointer transition-all shadow-lg shadow-orange-950/50 active:scale-[0.98]"
              >
                <ShoppingBag class="w-4 h-4" />
                <span>{{ t.addToCart }} ({{ (product.price * quantity) }} ₾)</span>
              </button>
            </div>
          </div>

        </div>

      </div>

    </div>
  </div>
</template>
