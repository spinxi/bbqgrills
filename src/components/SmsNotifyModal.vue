<script setup>
import { ref, computed } from 'vue'
import { X, Bell, CheckCircle2, Smartphone, ShieldCheck, Zap } from 'lucide-vue-next'

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

const emit = defineEmits(['close', 'sms-subscribed'])

const phoneNumber = ref('+995 5')
const selectedBranchPref = ref('any')
const isSubmitting = ref(false)
const isSuccess = ref(false)
const errorMessage = ref('')

const localizedTitle = computed(() => {
  if (!props.product) return ''
  return props.product.title[props.currentLang] || props.product.title.ka
})

const handlePhoneInput = (e) => {
  let val = e.target.value
  if (!val.startsWith('+995')) {
    val = '+995 '
  }
  phoneNumber.value = val
  errorMessage.value = ''
}

const handleSubmit = () => {
  // Validate Georgian phone number (e.g. +995 5XX XX XX XX, length >= 12)
  const cleanDigits = phoneNumber.value.replace(/\D/g, '')
  if (cleanDigits.length < 12) {
    errorMessage.value = 'გთხოვთ შეიყვანოთ სწორი ქართული მობილური ნომერი (+995 5XX...)'
    return
  }

  isSubmitting.value = true
  setTimeout(() => {
    isSubmitting.value = false
    isSuccess.value = true
    emit('sms-subscribed', {
      phone: phoneNumber.value,
      product: props.product,
      branch: selectedBranchPref.value
    })
  }, 700)
}

const closeAndReset = () => {
  isSuccess.value = false
  phoneNumber.value = '+995 5'
  errorMessage.value = ''
  emit('close')
}
</script>

<template>
  <div 
    v-if="isOpen && product"
    class="fixed inset-0 z-50 overflow-y-auto bg-black/85 backdrop-blur-md flex items-center justify-center p-3 sm:p-6"
    @click.self="closeAndReset"
  >
    <div class="relative w-full max-w-lg bg-zinc-900 border border-zinc-700/80 rounded-2xl shadow-2xl overflow-hidden my-auto animate-in fade-in zoom-in-95 duration-200">
      
      <!-- Modal Header -->
      <div class="bg-zinc-950 px-6 py-5 border-b border-zinc-800 flex items-start justify-between gap-4">
        <div class="flex items-center gap-3">
          <div class="w-10 h-10 rounded-xl bg-red-600/20 text-red-400 border border-red-500/30 flex items-center justify-center shrink-0">
            <Bell class="w-5 h-5 animate-bounce" />
          </div>
          <div>
            <h2 class="text-lg font-bold text-white">
              {{ t.smsModalTitle }}
            </h2>
            <p class="text-xs text-zinc-400">
              {{ currentLang === 'ka' ? 'ავტომატური შეტყობინება მარაგის შევსებისას' : (currentLang === 'ru' ? 'Автоматическое SMS при пополнении склада' : 'Instant automated SMS restock alert') }}
            </p>
          </div>
        </div>

        <button 
          @click="closeAndReset"
          class="p-2 rounded-xl bg-zinc-800/80 hover:bg-zinc-700 text-zinc-400 hover:text-white transition-colors cursor-pointer"
        >
          <X class="w-5 h-5" />
        </button>
      </div>

      <!-- Success State -->
      <div v-if="isSuccess" class="p-8 text-center space-y-4">
        <div class="w-16 h-16 rounded-full bg-emerald-500/20 text-emerald-400 border border-emerald-500/40 flex items-center justify-center mx-auto">
          <CheckCircle2 class="w-8 h-8" />
        </div>
        <h3 class="text-xl font-bold text-white">{{ currentLang === 'ka' ? 'შეტყობინება გააქტიურებულია!' : (currentLang === 'ru' ? 'Уведомление активировано!' : 'Restock Alert Activated!') }}</h3>
        <p class="text-sm text-zinc-300">
          {{ currentLang === 'ka' ? `ტელეფონის ნომერი ${phoneNumber} დარეგისტრირდა. როგორც კი პროდუქტი ${localizedTitle} საწყობში შემოვა, მომენტალურად მიიღებთ უფასო SMS-ს.` : (currentLang === 'ru' ? `Номер телефона ${phoneNumber} зарегистрирован. Как только ${localizedTitle} поступит на склад, вы получите мгновенное бесплатное SMS.` : `Phone number ${phoneNumber} registered. As soon as ${localizedTitle} arrives in stock, you will receive an instant free SMS alert.`) }}
        </p>
        <button
          @click="closeAndReset"
          class="px-6 py-2.5 rounded-xl bg-red-600 hover:bg-red-500 text-white font-bold text-sm cursor-pointer shadow-lg shadow-red-950/40"
        >
          {{ currentLang === 'ka' ? 'დასრულება' : (currentLang === 'ru' ? 'Готово' : 'Done') }}
        </button>
      </div>

      <!-- Form State -->
      <div v-else class="p-6 space-y-5">
        
        <!-- Product Thumbnail & Notice -->
        <div class="flex items-center gap-3 p-3 rounded-xl bg-zinc-950 border border-zinc-800">
          <img 
            :src="product.image" 
            :alt="localizedTitle" 
            class="w-12 h-12 rounded-lg object-contain bg-zinc-900 border border-zinc-800 p-1"
          />
          <div class="flex-1 min-w-0">
            <div class="text-[11px] font-bold text-red-400 uppercase tracking-wider">{{ product.brand }}</div>
            <div class="text-xs font-bold text-white truncate">{{ localizedTitle }}</div>
            <div class="text-[11px] text-zinc-400 font-mono mt-0.5">{{ product.price.toLocaleString() }} ₾</div>
          </div>
        </div>

        <p class="text-xs text-zinc-300 leading-relaxed">
          {{ t.smsModalDesc }}
        </p>

        <!-- Phone input -->
        <div>
          <label class="block text-xs font-bold text-zinc-300 uppercase tracking-wider mb-1.5">
            {{ t.phoneLabel }}
          </label>
          <div class="relative">
            <Smartphone class="w-4 h-4 text-zinc-500 absolute left-3.5 top-1/2 -translate-y-1/2" />
            <input 
              type="tel"
              :value="phoneNumber"
              @input="handlePhoneInput"
              placeholder="+995 5XX XX XX XX"
              class="w-full bg-zinc-950 border border-zinc-800 focus:border-red-500 focus:ring-1 focus:ring-red-500 rounded-xl pl-10 pr-4 py-2.5 text-sm text-white font-mono placeholder-zinc-500 outline-none transition-all"
            />
          </div>
          <div v-if="errorMessage" class="text-xs text-red-400 mt-1">
            {{ errorMessage }}
          </div>
        </div>

        <!-- Branch Preference -->
        <div>
          <label class="block text-xs font-bold text-zinc-300 uppercase tracking-wider mb-1.5">
            {{ t.branchPreference }}
          </label>
          <div class="grid grid-cols-1 gap-2">
            <label class="flex items-center gap-2.5 p-2.5 rounded-xl bg-zinc-950 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <input 
                type="radio" 
                name="branch-pref" 
                value="any" 
                v-model="selectedBranchPref" 
                class="accent-red-600" 
              />
              <span class="text-xs text-zinc-200">{{ t.anyBranch }}</span>
            </label>
            <label class="flex items-center gap-2.5 p-2.5 rounded-xl bg-zinc-950 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <input 
                type="radio" 
                name="branch-pref" 
                value="beliashvili" 
                v-model="selectedBranchPref" 
                class="accent-red-600" 
              />
              <span class="text-xs text-zinc-200">{{ currentLang === 'ka' ? 'მხოლოდ ბელიაშვილის 43 შოურუმში' : (currentLang === 'ru' ? 'Только в шоуруме Белиашвили 43' : 'Only at 43 Beliashvili Showroom') }}</span>
            </label>
            <label class="flex items-center gap-2.5 p-2.5 rounded-xl bg-zinc-950 border border-zinc-800 cursor-pointer hover:border-zinc-700">
              <input 
                type="radio" 
                name="branch-pref" 
                value="citymall" 
                v-model="selectedBranchPref" 
                class="accent-red-600" 
              />
              <span class="text-xs text-zinc-200">{{ currentLang === 'ka' ? 'მხოლოდ სითი მოლი საბურთალოში' : (currentLang === 'ru' ? 'Только в City Mall Сабуртало' : 'Only at City Mall Saburtalo') }}</span>
            </label>
          </div>
        </div>

        <!-- Submit Button -->
        <button
          @click="handleSubmit"
          :disabled="isSubmitting"
          class="w-full py-3 rounded-xl bg-red-600 hover:bg-red-500 text-white font-bold text-sm transition-all duration-150 cursor-pointer shadow-lg shadow-red-950/40 flex items-center justify-center gap-2 disabled:opacity-60"
        >
          <Bell class="w-4 h-4" />
          <span>{{ isSubmitting ? (currentLang === 'ka' ? 'რეგისტრაცია...' : (currentLang === 'ru' ? 'Регистрация...' : 'Subscribing...')) : t.subscribeSmsBtn }}</span>
        </button>

      </div>

    </div>
  </div>
</template>
