<script setup>
import { ref, computed } from 'vue'
import { 
  X, 
  ShoppingBag, 
  Trash2, 
  Plus, 
  Minus, 
  Truck, 
  CheckCircle2, 
  ArrowRight,
  Tag
} from 'lucide-vue-next'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },
  items: {
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

const emit = defineEmits([
  'close', 
  'update-quantity', 
  'remove-item', 
  'clear-cart',
  'checkout-completed'
])

const fulfillmentType = ref('delivery')
const promoCode = ref('')
const promoApplied = ref(false)
const isCheckingOut = ref(false)
const orderSuccess = ref(false)

const subtotal = computed(() => {
  return props.items.reduce((acc, item) => acc + (item.product.price * item.quantity), 0)
})

const discount = computed(() => {
  if (promoApplied.value) {
    return Math.round(subtotal.value * 0.05)
  }
  return 0
})

const shippingFee = computed(() => {
  if (fulfillmentType.value !== 'delivery') return 0
  if (subtotal.value >= 150) return 0
  return 15
})

const finalTotal = computed(() => {
  return Math.max(0, subtotal.value - discount.value + shippingFee.value)
})

const applyPromo = () => {
  if (promoCode.value.trim().toUpperCase() === 'GRILL2025' || promoCode.value.trim().toUpperCase() === 'VIP') {
    promoApplied.value = true
  }
}

const proceedToCheckout = () => {
  isCheckingOut.value = true
  setTimeout(() => {
    isCheckingOut.value = false
    orderSuccess.value = true
    emit('checkout-completed', {
      total: finalTotal.value,
      fulfillment: fulfillmentType.value,
      itemsCount: props.items.length
    })
  }, 900)
}

const closeDrawer = () => {
  orderSuccess.value = false
  emit('close')
}
</script>

<template>
  <div>
    <!-- Backdrop -->
    <div 
      v-if="isOpen"
      class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm transition-opacity"
      @click="closeDrawer"
    ></div>

    <!-- Drawer Panel -->
    <aside
      aria-label="Shopping Cart Drawer"
      class="fixed inset-y-0 right-0 z-50 w-full max-w-md bg-zinc-950 border-l border-zinc-800 shadow-2xl flex flex-col transform transition-transform duration-300 ease-in-out"
      :class="isOpen ? 'translate-x-0' : 'translate-x-full'"
    >
      <!-- Header -->
      <div class="p-6 border-b border-zinc-900 flex items-center justify-between">
        <div class="flex items-center gap-2.5">
          <ShoppingBag class="w-5 h-5 text-white" />
          <h2 class="text-base font-bold text-white">{{ t.cartTitle }}</h2>
          <span class="text-xs text-zinc-500 font-mono">({{ items.length }})</span>
        </div>

        <button 
          @click="closeDrawer"
          class="p-2 rounded-full hover:bg-zinc-900 text-zinc-400 hover:text-white transition-colors cursor-pointer"
        >
          <X class="w-5 h-5" />
        </button>
      </div>

      <!-- Order Placed Success View -->
      <div v-if="orderSuccess" class="p-8 text-center my-auto space-y-4">
        <div class="w-16 h-16 rounded-full bg-emerald-500/20 text-emerald-400 border border-emerald-500/40 flex items-center justify-center mx-auto">
          <CheckCircle2 class="w-8 h-8" />
        </div>
        <h3 class="text-xl font-bold text-white">შეკვეთა მიღებულია!</h3>
        <p class="text-sm text-zinc-300">
          გმადლობთ! თქვენი შეკვეთა წარმატებით გაფორმდა.
          Grills.ge-ს მენეჯერი დაგიკავშირდებათ უახლოეს წუთებში.
        </p>
        <button
          @click="closeDrawer"
          class="px-6 py-2.5 rounded-full bg-white hover:bg-zinc-200 text-zinc-950 font-bold text-xs cursor-pointer"
        >
          გაგრძელება
        </button>
      </div>

      <!-- Empty Cart State -->
      <div v-else-if="items.length === 0" class="p-8 text-center my-auto space-y-3">
        <div class="w-16 h-16 rounded-full bg-zinc-900 text-zinc-600 flex items-center justify-center mx-auto">
          <ShoppingBag class="w-8 h-8" />
        </div>
        <h3 class="text-base font-semibold text-white">{{ t.emptyCartTitle }}</h3>
        <p class="text-xs text-zinc-400 max-w-xs mx-auto">{{ t.emptyCartSubtitle }}</p>
        <button
          @click="closeDrawer"
          class="mt-3 px-6 py-2.5 rounded-full bg-white text-zinc-950 text-xs font-bold transition-all cursor-pointer hover:bg-zinc-200"
        >
          {{ t.ctaExplore }}
        </button>
      </div>

      <!-- Items List & Checkout Body -->
      <div v-else class="flex-1 overflow-y-auto p-6 space-y-5">
        
        <!-- Free Shipping Notice -->
        <div class="p-3.5 rounded-xl bg-zinc-900/60 border border-zinc-800 text-xs">
          <div class="flex items-center gap-2 text-zinc-300 font-medium">
            <Truck class="w-4 h-4 text-emerald-400 shrink-0" />
            <span>{{ subtotal >= 150 ? (currentLang === 'ka' ? 'უფასო მიწოდება საქართველოს მასშტაბით' : (currentLang === 'ru' ? 'Бесплатная доставка по Грузии' : 'Free delivery across Georgia')) : (currentLang === 'ka' ? `დაამატეთ ${150 - subtotal} ₾ უფასო მიწოდებისთვის` : (currentLang === 'ru' ? `Добавьте еще ${150 - subtotal} ₾ для бесплатной доставки` : `Add ${150 - subtotal} ₾ for free delivery`)) }}</span>
          </div>
        </div>

        <!-- Product Items -->
        <div class="space-y-3">
          <div 
            v-for="item in items" 
            :key="item.product.id"
            class="p-3 rounded-xl bg-zinc-900/40 border border-zinc-800/80 flex gap-3.5 items-center"
          >
            <!-- Image -->
            <img 
              :src="item.product.image" 
              :alt="item.product.title.ka" 
              class="w-16 h-16 rounded-lg object-cover border border-zinc-800 shrink-0"
            />

            <!-- Info -->
            <div class="flex-1 min-w-0">
              <div class="text-[10px] font-bold text-zinc-500 uppercase tracking-wider">{{ item.product.brand }}</div>
              <div class="text-xs font-semibold text-white truncate">
                {{ item.product.title[currentLang] || item.product.title.ka }}
              </div>
              <div class="text-xs font-mono font-bold text-zinc-200 mt-0.5">
                {{ item.product.price.toLocaleString() }} ₾
              </div>

              <!-- Quantity changer -->
              <div class="flex items-center gap-2 mt-2">
                <div class="flex items-center border border-zinc-800 bg-zinc-900 rounded-lg">
                  <button 
                    @click="emit('update-quantity', item.product.id, item.quantity - 1)"
                    class="p-1 text-zinc-400 hover:text-white cursor-pointer"
                  >
                    <Minus class="w-3 h-3" />
                  </button>
                  <span class="px-2 text-xs font-bold font-mono text-white">{{ item.quantity }}</span>
                  <button 
                    @click="emit('update-quantity', item.product.id, item.quantity + 1)"
                    class="p-1 text-zinc-400 hover:text-white cursor-pointer"
                  >
                    <Plus class="w-3 h-3" />
                  </button>
                </div>

                <button 
                  @click="emit('remove-item', item.product.id)"
                  class="text-zinc-500 hover:text-red-400 p-1 cursor-pointer transition-colors"
                  title="Remove"
                >
                  <Trash2 class="w-3.5 h-3.5" />
                </button>
              </div>
            </div>

            <!-- Item subtotal -->
            <div class="text-right text-xs font-mono font-bold text-white shrink-0">
              {{ (item.product.price * item.quantity).toLocaleString() }} ₾
            </div>
          </div>
        </div>

        <!-- Fulfillment Method -->
        <div class="space-y-2 pt-2 border-t border-zinc-900">
          <label class="block text-xs font-semibold text-zinc-400 uppercase tracking-wider">
            {{ t.selectPickupOrDelivery }}
          </label>
          <div class="space-y-1.5 text-xs">
            <label class="flex items-center gap-2.5 p-2.5 rounded-xl bg-zinc-900/60 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <input type="radio" value="delivery" v-model="fulfillmentType" class="accent-red-600" />
              <div class="flex items-center justify-between w-full">
                <span>{{ t.deliveryTbilisi }}</span>
                <span class="font-mono text-emerald-400 font-medium">{{ shippingFee === 0 ? t.free : '15 ₾' }}</span>
              </div>
            </label>
            <label class="flex items-center gap-2.5 p-2.5 rounded-xl bg-zinc-900/60 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <input type="radio" value="pickup-beliashvili" v-model="fulfillmentType" class="accent-red-600" />
              <div class="flex items-center justify-between w-full">
                <span>{{ t.pickupBeliashvili }}</span>
                <span class="font-mono text-emerald-400 font-medium">{{ t.free }}</span>
              </div>
            </label>
            <label class="flex items-center gap-2.5 p-2.5 rounded-xl bg-zinc-900/60 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <input type="radio" value="pickup-citymall" v-model="fulfillmentType" class="accent-red-600" />
              <div class="flex items-center justify-between w-full">
                <span>{{ t.pickupCityMall }}</span>
                <span class="font-mono text-emerald-400 font-medium">{{ t.free }}</span>
              </div>
            </label>
          </div>
        </div>

      </div>

      <!-- Drawer Footer with Total & Clean Payment Method Badges -->
      <div v-if="items.length > 0 && !orderSuccess" class="p-6 bg-zinc-950 border-t border-zinc-900 space-y-4">
        
        <!-- Summary Breakdown -->
        <div class="space-y-1.5 text-xs text-zinc-400">
          <div class="flex justify-between">
            <span>{{ t.subtotal }}</span>
            <span class="font-mono text-zinc-200">{{ subtotal.toLocaleString() }} ₾</span>
          </div>
          <div class="flex justify-between">
            <span>{{ t.shipping }}</span>
            <span class="font-mono text-zinc-200">{{ shippingFee === 0 ? t.free : `${shippingFee} ₾` }}</span>
          </div>
          <div class="flex justify-between text-base font-bold text-white pt-2 border-t border-zinc-900">
            <span>{{ t.totalPayable }}</span>
            <span class="font-mono text-xl">{{ finalTotal.toLocaleString() }} ₾</span>
          </div>
        </div>

        <!-- Checkout Button -->
        <button
          @click="proceedToCheckout"
          :disabled="isCheckingOut"
          class="w-full py-3.5 rounded-full bg-white hover:bg-zinc-200 text-zinc-950 font-bold text-sm flex items-center justify-center gap-2 cursor-pointer transition-all active:scale-[0.98] shadow-lg disabled:opacity-60"
        >
          <span>{{ isCheckingOut ? t.processing : t.checkoutBtn }}</span>
          <ArrowRight class="w-4 h-4" />
        </button>

        <!-- Clean Payment Method Badges Below Checkout Button -->
        <div class="pt-2 flex items-center justify-center gap-2 flex-wrap text-[11px] text-zinc-500">
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-zinc-400">Visa / Mastercard</span>
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-zinc-400">Apple Pay</span>
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-zinc-400">TBC განვადება</span>
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-zinc-400">BOG ნაწილ-ნაწილ</span>
        </div>

      </div>
    </aside>
  </div>
</template>
