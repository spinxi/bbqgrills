<script setup>
import { ref, computed, watch } from 'vue'
import { 
  X, 
  ShoppingBag, 
  MapPin, 
  ShieldCheck, 
  Bell,
  Plus,
  Minus,
  Clock,
  Check
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

const emit = defineEmits(['close', 'add-to-cart', 'open-sms'])

const activeImageIndex = ref(0)
const quantity = ref(1)
const isReserved = ref(false)

watch(() => props.product, () => {
  activeImageIndex.value = 0
  quantity.value = 1
  isReserved.value = false
})

const currentImage = computed(() => {
  if (!props.product) return ''
  if (props.product.gallery && props.product.gallery[activeImageIndex.value]) {
    return props.product.gallery[activeImageIndex.value]
  }
  return props.product.image
})

const totalStock = computed(() => {
  if (!props.product) return 0
  return (props.product.stockBeliashvili || 0) + (props.product.stockCityMall || 0)
})

const isOutOfStock = computed(() => {
  return totalStock.value <= 0
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
  return props.currentLang === 'ka' ? 'გრილი' : (props.currentLang === 'ru' ? 'Гриль' : 'Grill')
})

const handleAddToCart = () => {
  emit('add-to-cart', props.product, quantity.value)
  emit('close')
}

const handleReserve = () => {
  isReserved.value = true
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
      
      <!-- Close Button (Positioned cleanly with high z-index) -->
      <button 
        @click="emit('close')"
        class="absolute top-3 right-3 sm:top-4 sm:right-4 z-30 p-2 sm:p-2.5 rounded-full bg-zinc-950/90 hover:bg-zinc-800 text-zinc-400 hover:text-white transition-colors cursor-pointer border border-zinc-800 shadow-md"
        aria-label="Close"
      >
        <X class="w-4 h-4 sm:w-5 sm:h-5" />
      </button>

      <!-- Modal Content Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2">
        
        <!-- Left: Image Gallery & 3 Thumbnails Switcher -->
        <div class="p-4 sm:p-8 bg-zinc-950 flex flex-col justify-between border-b md:border-b-0 md:border-r border-zinc-800/80">
          <div class="space-y-4">
            <!-- Large Image Preview -->
            <div 
              class="relative w-full aspect-square rounded-2xl overflow-hidden border border-zinc-800 shadow-inner flex items-center justify-center p-3"
              :class="(product.isPNGStudio || (currentImage && currentImage.includes('grills.ge'))) 
                ? 'bg-gradient-to-b from-zinc-800 to-zinc-900' 
                : 'bg-zinc-900'"
            >
              <img 
                :src="currentImage" 
                :alt="localizedTitle" 
                class="transition-all duration-300"
                :class="(product.isPNGStudio || (currentImage && currentImage.includes('grills.ge'))) 
                  ? 'w-full h-full object-contain p-4' 
                  : 'w-full h-full object-cover'"
                @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?auto=format&fit=crop&w=800&q=80'"
              />
              <div class="absolute top-3.5 left-3.5 z-10">
                <span class="px-2.5 py-1 rounded-lg text-xs font-bold uppercase tracking-wider bg-zinc-950/85 text-white border border-zinc-700/80 backdrop-blur-sm">
                  {{ product.brand }}
                </span>
              </div>
            </div>

            <!-- 3 Thumbnails Switcher Below Main Image -->
            <div class="flex items-center gap-3">
              <button
                v-for="(img, idx) in (product.gallery || [product.image])"
                :key="idx"
                @click="activeImageIndex = idx"
                class="w-16 h-16 sm:w-20 sm:h-20 rounded-xl overflow-hidden border-2 transition-all cursor-pointer bg-zinc-900 flex items-center justify-center p-1"
                :class="activeImageIndex === idx 
                  ? 'border-red-600 scale-105 shadow-md shadow-red-950/40' 
                  : 'border-zinc-800 hover:border-zinc-600 opacity-60 hover:opacity-100'"
              >
                <img 
                  :src="img" 
                  alt="Product view" 
                  class="w-full h-full object-contain"
                  @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?auto=format&fit=crop&w=800&q=80'"
                />
              </button>
            </div>
          </div>

          <!-- Official Distributor Guarantee Note -->
          <div class="mt-4 p-3 rounded-xl bg-zinc-900/60 border border-zinc-800 text-xs text-zinc-400 flex items-center gap-2.5">
            <ShieldCheck class="w-4 h-4 text-emerald-400 shrink-0" />
            <span>{{ t.officialDistributorGuarantee }}</span>
          </div>
        </div>

        <!-- Right: Details, Stock Status, Specs Table & Actions -->
        <div class="p-4 sm:p-8 flex flex-col justify-between space-y-5 sm:space-y-6">
          
          <div class="space-y-4">
            
            <!-- Brand, Origin & SKU Code (with pr-12 to prevent overlap with close button) -->
            <div class="flex items-center justify-between text-[11px] text-zinc-500 font-mono pr-12">
              <div class="flex items-center gap-2">
                <span class="font-bold text-zinc-400 uppercase tracking-wider">{{ product.brand }} ({{ product.brandOrigin }})</span>
                <span>•</span>
                <span class="text-red-500 font-semibold">{{ localizedCategory }}</span>
              </div>
              <div class="bg-zinc-800/80 px-2 py-0.5 rounded text-zinc-300 border border-zinc-700/60">
                SKU: {{ product.sku || 'WB-1500014' }}
              </div>
            </div>

            <!-- Title & Subtitle -->
            <div>
              <h2 class="text-xl sm:text-2xl font-bold text-white leading-snug">
                {{ localizedTitle }}
              </h2>
              <p class="text-xs text-zinc-400 mt-1 leading-relaxed">
                {{ localizedSubtitle }}
              </p>
            </div>

            <!-- Price -->
            <div class="flex items-baseline gap-3 pb-3 border-b border-zinc-800">
              <span class="text-2xl sm:text-3xl font-bold text-white font-mono">
                {{ product.price.toLocaleString() }} ₾
              </span>
              <span 
                v-if="product.oldPrice" 
                class="text-sm text-zinc-500 line-through font-mono"
              >
                {{ product.oldPrice.toLocaleString() }} ₾
              </span>
            </div>

            <!-- Showroom Stock Status (Full-Width Horizontal Flex Bars, No Wrapping) -->
            <div class="space-y-2">
              <div class="text-xs font-semibold text-zinc-300 flex items-center gap-1.5">
                <MapPin class="w-3.5 h-3.5 text-red-500" />
                <span>{{ t.showroomAvailabilityHeading }}</span>
              </div>
              <div class="space-y-2 text-xs">
                <!-- Beliashvili 43 Row -->
                <div class="flex items-center justify-between px-4 py-2.5 bg-zinc-900/80 rounded-xl border border-zinc-800">
                  <div class="flex items-center gap-2">
                    <span 
                      class="w-2 h-2 rounded-full shrink-0" 
                      :class="product.stockBeliashvili > 0 ? 'bg-emerald-400' : 'bg-zinc-600'"
                    ></span>
                    <span class="text-zinc-300 font-medium">{{ t.showroomBeliashviliBadge }}</span>
                  </div>
                  <span 
                    class="font-medium font-mono whitespace-nowrap px-2.5 py-0.5 rounded-full text-[11px]"
                    :class="product.stockBeliashvili > 0 
                      ? 'bg-emerald-950/80 text-emerald-300 border border-emerald-800/60' 
                      : 'bg-zinc-800 text-zinc-500 border border-zinc-700'"
                  >
                    {{ product.stockBeliashvili > 0 ? `${t.inStockWithCount} (${product.stockBeliashvili} ${t.stockUnits})` : t.outOfStock }}
                  </span>
                </div>

                <!-- City Mall Saburtalo Row -->
                <div class="flex items-center justify-between px-4 py-2.5 bg-zinc-900/80 rounded-xl border border-zinc-800">
                  <div class="flex items-center gap-2">
                    <span 
                      class="w-2 h-2 rounded-full shrink-0" 
                      :class="product.stockCityMall > 0 ? 'bg-emerald-400' : 'bg-zinc-600'"
                    ></span>
                    <span class="text-zinc-300 font-medium">{{ t.showroomCityMallBadge }}</span>
                  </div>
                  <span 
                    class="font-medium font-mono whitespace-nowrap px-2.5 py-0.5 rounded-full text-[11px]"
                    :class="product.stockCityMall > 0 
                      ? 'bg-emerald-950/80 text-emerald-300 border border-emerald-800/60' 
                      : 'bg-zinc-800 text-zinc-500 border border-zinc-700'"
                  >
                    {{ product.stockCityMall > 0 ? `${t.inStockWithCount} (${product.stockCityMall} ${t.stockUnits})` : t.outOfStock }}
                  </span>
                </div>
              </div>
            </div>

            <!-- Technical Specifications Table (ტექნიკური მახასიათებლები) -->
            <div class="space-y-2 pt-1">
              <div class="text-xs font-semibold text-zinc-300">
                {{ t.techSpecs }}
              </div>
              <div class="rounded-xl border border-zinc-800 bg-zinc-950/60 overflow-hidden divide-y divide-zinc-800/80 text-xs">
                <div class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specDiameter }}</span>
                  <span class="font-medium text-zinc-200">{{ product.specs.diameter }}</span>
                </div>
                <div class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specFuel }}</span>
                  <span class="font-medium text-zinc-200">{{ product.specs.fuel }}</span>
                </div>
                <div class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specMaterial }}</span>
                  <span class="font-medium text-zinc-200">{{ product.specs.material }}</span>
                </div>
                <div class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specWeight }}</span>
                  <span class="font-medium text-zinc-200 font-mono">{{ product.specs.weight }}</span>
                </div>
                <div class="px-3.5 py-2 flex justify-between">
                  <span class="text-zinc-400">{{ t.specWarranty }}</span>
                  <span class="font-medium text-red-500">{{ product.specs.warranty }}</span>
                </div>
              </div>
            </div>

          </div>

          <!-- Bottom Actions: Quantity Selector + Crimson Red Add to Cart + Showroom Reservation -->
          <div class="space-y-2 pt-2">
            <div v-if="!isOutOfStock" class="flex items-center gap-3">
              <!-- Quantity Counter (- 1 +) -->
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

              <!-- Add to Cart Button (Crimson Red Accent) -->
              <button
                @click="handleAddToCart"
                class="flex-1 py-3.5 px-6 rounded-xl bg-red-600 hover:bg-red-500 text-white font-bold text-sm flex items-center justify-center gap-2 cursor-pointer transition-all shadow-lg shadow-red-600/20 active:scale-[0.98]"
              >
                <ShoppingBag class="w-4 h-4" />
                <span>{{ t.addToCart }} ({{ (product.price * quantity).toLocaleString() }} ₾)</span>
              </button>
            </div>

            <!-- If out of stock: SMS Notification Button -->
            <button
              v-else
              @click="emit('open-sms', product); emit('close')"
              class="w-full py-3.5 rounded-xl bg-zinc-900 hover:bg-zinc-800 border border-zinc-700 text-zinc-200 text-sm font-semibold flex items-center justify-center gap-2 cursor-pointer transition-all"
            >
              <Bell class="w-4 h-4 text-zinc-400" />
              <span>{{ t.notifySms }}</span>
            </button>

            <!-- Showroom 1-Hour Reservation Button -->
            <button
              v-if="!isOutOfStock && !isReserved"
              @click="handleReserve"
              class="w-full py-2.5 rounded-xl bg-zinc-900 hover:bg-zinc-800/80 border border-zinc-800 text-zinc-300 hover:text-white text-xs font-semibold flex items-center justify-center gap-2 cursor-pointer transition-colors"
            >
              <Clock class="w-3.5 h-3.5 text-red-500" />
              <span>{{ t.reserveShowroom }}</span>
            </button>

            <div 
              v-else-if="isReserved"
              class="p-2.5 rounded-xl bg-emerald-950/60 border border-emerald-800/60 text-xs text-emerald-300 flex items-center justify-center gap-2"
            >
              <Check class="w-4 h-4 text-emerald-400" />
              <span>{{ t.reservationConfirmed }} #RES-{{ Math.floor(1000 + Math.random() * 9000) }} {{ t.inShowroom }}</span>
            </div>
          </div>

        </div>

      </div>

    </div>
  </div>
</template>
