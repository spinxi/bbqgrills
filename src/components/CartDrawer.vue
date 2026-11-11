<script setup>
import { ref, computed } from 'vue'
import { 
  X, 
  ShoppingBag, 
  Trash2, 
  Plus, 
  Minus, 
  Truck, 
  CreditCard,
  Banknote,
  CheckCircle2, 
  ArrowRight
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

const deliveryType = ref('tbilisi') // 'self' | 'tbilisi' | 'regions'
const paymentMethod = ref('online') // 'online' | 'cod'
const isCheckingOut = ref(false)
const orderSuccess = ref(false)
const lastOrderRef = ref('')

const subtotal = computed(() => {
  return props.items.reduce((acc, item) => acc + (item.product.price * item.quantity), 0)
})

const deliveryFee = computed(() => {
  if (deliveryType.value === 'self') return 0
  if (deliveryType.value === 'tbilisi') return 20
  if (deliveryType.value === 'regions') return 35
  return 20
})

const finalTotal = computed(() => {
  return subtotal.value + deliveryFee.value
})

const proceedToCheckout = () => {
  isCheckingOut.value = true
  setTimeout(() => {
    isCheckingOut.value = false
    lastOrderRef.value = 'BBQ-' + Math.floor(10000 + Math.random() * 90000)
    orderSuccess.value = true
    emit('checkout-completed', {
      orderId: lastOrderRef.value,
      total: finalTotal.value,
      deliveryType: deliveryType.value,
      paymentMethod: paymentMethod.value,
      items: props.items
    })
  }, 600)
}

const closeDrawer = () => {
  if (orderSuccess.value) {
    orderSuccess.value = false
    emit('clear-cart')
  }
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
      <div class="p-5 sm:p-6 border-b border-zinc-900 flex items-center justify-between">
        <div class="flex items-center gap-2.5">
          <ShoppingBag class="w-5 h-5 text-orange-500" />
          <h2 class="text-base font-bold text-white">{{ t.cartTitle }}</h2>
          <span v-if="!orderSuccess" class="text-xs text-zinc-500 font-mono">({{ items.length }})</span>
        </div>

        <button 
          @click="closeDrawer"
          class="p-2 rounded-full hover:bg-zinc-900 text-zinc-400 hover:text-white transition-colors cursor-pointer"
        >
          <X class="w-5 h-5" />
        </button>
      </div>

      <!-- SUCCESS VIEW -->
      <div v-if="orderSuccess" class="p-8 text-center my-auto space-y-5">
        <div class="w-16 h-16 rounded-full bg-emerald-500/20 text-emerald-400 border border-emerald-500/40 flex items-center justify-center mx-auto">
          <CheckCircle2 class="w-8 h-8" />
        </div>
        <div class="space-y-1">
          <span class="text-xs font-mono text-orange-400 font-bold uppercase tracking-wider">
            {{ t.orderRefLabel }} {{ lastOrderRef }}
          </span>
          <h3 class="text-xl font-bold text-white">{{ t.orderSuccessTitle }}</h3>
          <p class="text-xs sm:text-sm text-zinc-300 leading-relaxed max-w-sm mx-auto">
            {{ t.orderSuccessSubtitle }}
          </p>
        </div>

        <div class="p-4 rounded-2xl bg-zinc-900/80 border border-zinc-800 text-left text-xs space-y-2">
          <div class="flex justify-between text-zinc-400">
            <span>{{ t.selectDeliveryMethod }}</span>
            <span class="font-bold text-white">
              {{ deliveryType === 'self' ? t.deliverySelfOpt : (deliveryType === 'tbilisi' ? t.deliveryTbilisiOpt : t.deliveryRegionsOpt) }}
            </span>
          </div>
          <div class="flex justify-between text-zinc-400">
            <span>{{ t.selectPaymentMethod }}</span>
            <span class="font-bold text-white">
              {{ paymentMethod === 'online' ? t.paymentOnlineOpt : t.paymentCodOpt }}
            </span>
          </div>
          <div class="flex justify-between text-zinc-400 border-t border-zinc-800 pt-2 font-bold text-sm">
            <span>{{ t.totalPayable }}</span>
            <span class="text-orange-400 font-mono">{{ finalTotal }} ₾</span>
          </div>
        </div>

        <button
          @click="closeDrawer"
          class="w-full py-3 rounded-xl bg-orange-600 hover:bg-orange-500 text-white text-xs font-bold transition-all cursor-pointer shadow-lg shadow-orange-950/40"
        >
          {{ t.finishBtn }}
        </button>
      </div>

      <!-- EMPTY STATE -->
      <div v-else-if="items.length === 0" class="p-8 text-center my-auto space-y-3">
        <div class="w-16 h-16 rounded-full bg-zinc-900 text-zinc-600 flex items-center justify-center mx-auto">
          <ShoppingBag class="w-8 h-8" />
        </div>
        <h3 class="text-base font-semibold text-white">{{ t.emptyCartTitle }}</h3>
        <p class="text-xs text-zinc-400 max-w-xs mx-auto">{{ t.emptyCartSubtitle }}</p>
        <button
          @click="closeDrawer"
          class="mt-3 px-6 py-2.5 rounded-full bg-orange-600 hover:bg-orange-500 text-white text-xs font-bold transition-all cursor-pointer"
        >
          {{ t.ctaExplore }}
        </button>
      </div>

      <!-- MAIN CART VIEW (Single View, No Extra Forms) -->
      <div v-else class="flex-1 overflow-y-auto p-5 sm:p-6 space-y-5">
        
        <!-- Fast delivery notice -->
        <div class="p-3.5 rounded-xl bg-zinc-900/80 border border-zinc-800 text-xs text-zinc-300 flex items-center gap-2">
          <Truck class="w-4 h-4 text-orange-400 shrink-0" />
          <span>{{ t.fastDeliveryBanner }}</span>
        </div>

        <!-- Cart Items List -->
        <div class="space-y-3">
          <div 
            v-for="item in items" 
            :key="item.product.id"
            class="p-3 rounded-2xl bg-zinc-900/60 border border-zinc-800 flex gap-3.5 items-center"
          >
            <!-- Image -->
            <img 
              :src="item.product.image" 
              :alt="item.product.title[currentLang] || item.product.title.ka" 
              class="w-16 h-16 rounded-xl object-cover border border-zinc-800 shrink-0"
            />

            <!-- Info -->
            <div class="flex-1 min-w-0">
              <div class="text-[10px] font-bold text-orange-400 font-mono tracking-wider">{{ item.product.sku }}</div>
              <div class="text-xs font-bold text-white truncate">
                {{ item.product.title[currentLang] || item.product.title.ka }}
              </div>
              <div class="text-xs font-mono font-bold text-zinc-200 mt-0.5">
                {{ item.product.price }} ₾
              </div>

              <!-- Quantity changer -->
              <div class="flex items-center gap-2 mt-2">
                <div class="flex items-center border border-zinc-800 bg-zinc-950 rounded-lg">
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
              {{ (item.product.price * item.quantity) }} ₾
            </div>
          </div>
        </div>

        <!-- 1. Delivery Choice Radio Buttons -->
        <div class="space-y-2 pt-2 border-t border-zinc-900">
          <label class="text-[11px] font-bold text-zinc-400 uppercase tracking-wider block">
            {{ t.selectDeliveryMethod }}
          </label>
          <div class="space-y-1.5 text-xs">
            <label class="flex items-center justify-between p-2.5 rounded-xl bg-zinc-900/60 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <div class="flex items-center gap-2">
                <input type="radio" value="tbilisi" v-model="deliveryType" class="accent-orange-500" />
                <span>{{ t.deliveryTbilisiOpt }}</span>
              </div>
              <span class="font-mono text-white font-bold">20 ₾</span>
            </label>

            <label class="flex items-center justify-between p-2.5 rounded-xl bg-zinc-900/60 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <div class="flex items-center gap-2">
                <input type="radio" value="regions" v-model="deliveryType" class="accent-orange-500" />
                <span>{{ t.deliveryRegionsOpt }}</span>
              </div>
              <span class="font-mono text-white font-bold">35 ₾</span>
            </label>

            <label class="flex items-center justify-between p-2.5 rounded-xl bg-zinc-900/60 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <div class="flex items-center gap-2">
                <input type="radio" value="self" v-model="deliveryType" class="accent-orange-500" />
                <span>{{ t.deliverySelfOpt }}</span>
              </div>
              <span class="font-mono text-emerald-400 font-bold">{{ t.free }}</span>
            </label>
          </div>
        </div>

        <!-- 2. Payment Choice Radio Buttons (Online & COD - No TBC transfer) -->
        <div class="space-y-2 pt-2 border-t border-zinc-900">
          <label class="text-[11px] font-bold text-zinc-400 uppercase tracking-wider block">
            {{ t.selectPaymentMethod }}
          </label>
          <div class="space-y-1.5 text-xs">
            <label class="flex items-center justify-between p-2.5 rounded-xl bg-zinc-900/60 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <div class="flex items-center gap-2">
                <input type="radio" value="online" v-model="paymentMethod" class="accent-orange-500" />
                <CreditCard class="w-3.5 h-3.5 text-cyan-400" />
                <span>{{ t.paymentOnlineOpt }}</span>
              </div>
            </label>

            <label class="flex items-center justify-between p-2.5 rounded-xl bg-zinc-900/60 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <div class="flex items-center gap-2">
                <input type="radio" value="cod" v-model="paymentMethod" class="accent-orange-500" />
                <Banknote class="w-3.5 h-3.5 text-emerald-400" />
                <span>{{ t.paymentCodOpt }}</span>
              </div>
            </label>
          </div>
        </div>

      </div>

      <!-- FOOTER SUMMARY & CHECKOUT BUTTON -->
      <div v-if="items.length > 0 && !orderSuccess" class="p-5 sm:p-6 bg-zinc-950 border-t border-zinc-900 space-y-3">
        
        <!-- Breakdown -->
        <div class="space-y-1.5 text-xs text-zinc-400">
          <div class="flex justify-between">
            <span>{{ t.subtotal }}</span>
            <span class="font-mono text-zinc-200">{{ subtotal }} ₾</span>
          </div>
          <div class="flex justify-between">
            <span>{{ t.shipping }}</span>
            <span class="font-mono text-zinc-200">{{ deliveryFee === 0 ? t.free : `${deliveryFee} ₾` }}</span>
          </div>
          <div class="flex justify-between text-base font-bold text-white pt-2 border-t border-zinc-900">
            <span>{{ t.totalPayable }}</span>
            <span class="font-mono text-xl text-orange-400">{{ finalTotal }} ₾</span>
          </div>
        </div>

        <!-- One-Click Checkout Button -->
        <div class="pt-1">
          <button
            @click="proceedToCheckout"
            :disabled="isCheckingOut"
            class="w-full py-3.5 rounded-2xl bg-orange-600 hover:bg-orange-500 text-white font-bold text-sm flex items-center justify-center gap-2 cursor-pointer transition-all shadow-lg shadow-orange-950/50 active:scale-[0.98] disabled:opacity-60"
          >
            <span>{{ isCheckingOut ? t.processing : t.checkoutBtn }}</span>
            <ArrowRight class="w-4 h-4" />
          </button>
        </div>

        <!-- Payment badges below button -->
        <div class="pt-1 flex items-center justify-center gap-2 flex-wrap text-[10px] text-zinc-500">
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-zinc-400">Visa / Mastercard</span>
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-zinc-400">Apple Pay</span>
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-zinc-400">Google Pay</span>
        </div>

      </div>
    </aside>
  </div>
</template>
