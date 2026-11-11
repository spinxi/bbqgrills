<script setup>
import { ref, computed } from 'vue'
import { 
  X, 
  CreditCard, 
  ShieldCheck, 
  CheckCircle2, 
  Sparkles, 
  Building, 
  Clock, 
  ArrowRight,
  Info
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

const emit = defineEmits(['close'])

const activeTab = ref('bog-part') // 'bog-part' or 'standard'
const selectedTerm = ref(12) // months
const downpaymentPercent = ref(0) // 0 to 50%
const isSubmitting = ref(false)
const isSubmitted = ref(false)

const applicantForm = ref({
  fullName: '',
  personalId: '',
  phone: '+995 '
})

const terms = [6, 12, 18, 24, 36]

const productPrice = computed(() => {
  return props.product ? props.product.price : 0
})

// BOG Part-by-part: 4 equal parts, 0% fee
const bogPaymentPerMonth = computed(() => {
  return Math.round(productPrice.value / 4)
})

// Standard Installment Calculations
const downpaymentAmount = computed(() => {
  return Math.round((productPrice.value * downpaymentPercent.value) / 100)
})

const financedAmount = computed(() => {
  return productPrice.value - downpaymentAmount.value
})

const standardMonthlyPayment = computed(() => {
  if (!financedAmount.value) return 0
  // Standard bank tariff formula (~12% per year nominal)
  const monthlyRate = 0.012
  const n = selectedTerm.value
  const monthly = (financedAmount.value * (monthlyRate * Math.pow(1 + monthlyRate, n))) / (Math.pow(1 + monthlyRate, n) - 1)
  return Math.round(monthly)
})

const totalPayableStandard = computed(() => {
  return downpaymentAmount.value + (standardMonthlyPayment.value * selectedTerm.value)
})

const localizedTitle = computed(() => {
  if (!props.product) return ''
  return props.product.title[props.currentLang] || props.product.title.ka
})

const submitApplication = () => {
  isSubmitting.value = true
  setTimeout(() => {
    isSubmitting.value = false
    isSubmitted.value = true
  }, 900)
}

const resetAndClose = () => {
  isSubmitted.value = false
  emit('close')
}
</script>

<template>
  <div 
    v-if="isOpen && product"
    class="fixed inset-0 z-50 overflow-y-auto bg-black/85 backdrop-blur-md flex items-center justify-center p-3 sm:p-6"
    @click.self="resetAndClose"
  >
    <div class="relative w-full max-w-3xl bg-zinc-900 border border-zinc-700/80 rounded-2xl shadow-2xl overflow-hidden my-auto animate-in fade-in zoom-in-95 duration-200">
      
      <!-- Modal Header -->
      <div class="bg-zinc-950 px-6 py-5 border-b border-zinc-800 flex items-start justify-between gap-4">
        <div class="flex items-center gap-3">
          <div class="w-10 h-10 rounded-xl bg-orange-600/20 text-orange-400 border border-orange-500/30 flex items-center justify-center shrink-0">
            <CreditCard class="w-5 h-5" />
          </div>
          <div>
            <h2 class="text-lg sm:text-xl font-bold text-white">
              {{ t.installmentModalTitle }}
            </h2>
            <p class="text-xs text-zinc-400">
              საქართველოს ბანკი (BOG) & TBC ბანკის ოფიციალური პარტნიორი
            </p>
          </div>
        </div>

        <button 
          @click="resetAndClose"
          class="p-2 rounded-xl bg-zinc-800/80 hover:bg-zinc-700 text-zinc-400 hover:text-white transition-colors cursor-pointer"
        >
          <X class="w-5 h-5" />
        </button>
      </div>

      <!-- Selected Product Preview Card -->
      <div class="px-6 py-4 bg-zinc-950/60 border-b border-zinc-800 flex items-center justify-between gap-4 flex-wrap">
        <div class="flex items-center gap-3">
          <img 
            :src="product.image" 
            :alt="localizedTitle" 
            class="w-14 h-14 rounded-lg object-cover border border-zinc-800"
          />
          <div>
            <div class="text-xs font-semibold text-orange-400 uppercase tracking-wider">{{ product.brand }}</div>
            <div class="text-sm font-bold text-white max-w-md truncate">{{ localizedTitle }}</div>
          </div>
        </div>
        <div class="text-right">
          <div class="text-xs text-zinc-400">ღირებულება:</div>
          <div class="text-xl font-black text-white font-mono">{{ product.price.toLocaleString() }} ₾</div>
        </div>
      </div>

      <!-- If application submitted successfully -->
      <div v-if="isSubmitted" class="p-8 text-center space-y-4">
        <div class="w-16 h-16 rounded-full bg-emerald-500/20 text-emerald-400 border border-emerald-500/40 flex items-center justify-center mx-auto">
          <CheckCircle2 class="w-8 h-8" />
        </div>
        <h3 class="text-xl font-bold text-white">განვადების მოთხოვნა მიღებულია!</h3>
        <p class="text-sm text-zinc-300 max-w-md mx-auto">
          თქვენი განაცხადი {{ activeTab === 'bog-part' ? 'BOG ნაწილ-ნაწილზე (0%)' : 'სტანდარტულ განვადებაზე' }} გადაეგზავნა ბანკის ონლაინ სისტემას. 
          დამტკიცების SMS-კოდი მოგივათ თქვენს ნომერზე 2-3 წუთში.
        </p>
        <button
          @click="resetAndClose"
          class="px-6 py-2.5 rounded-xl bg-orange-600 hover:bg-orange-500 text-white font-bold text-sm cursor-pointer"
        >
          დასრულება
        </button>
      </div>

      <!-- Calculator Body -->
      <div v-else class="p-6 space-y-6">
        
        <!-- Tabs: BOG Part-by-Part vs Standard Installment -->
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
          <button
            @click="activeTab = 'bog-part'"
            class="p-4 rounded-xl border text-left transition-all cursor-pointer relative"
            :class="activeTab === 'bog-part'
              ? 'bg-orange-950/40 border-orange-500 text-white shadow-lg shadow-orange-950/30'
              : 'bg-zinc-950/60 border-zinc-800 text-zinc-400 hover:text-zinc-200'"
          >
            <div class="flex items-center justify-between mb-1">
              <span class="text-xs font-bold text-orange-400 uppercase tracking-wider">0% საკომისიო</span>
              <span class="px-2 py-0.5 rounded text-[10px] font-mono bg-orange-600 text-white font-black">4 თვე</span>
            </div>
            <div class="text-sm font-bold text-white">BOG ნაწილ-ნაწილ (Part-by-Part)</div>
            <div class="text-xs text-zinc-400 mt-1">4 თანაბარი გადახდა ყოველგვარი ზედმეტობის გარეშე.</div>
          </button>

          <button
            @click="activeTab = 'standard'"
            class="p-4 rounded-xl border text-left transition-all cursor-pointer relative"
            :class="activeTab === 'standard'
              ? 'bg-amber-950/40 border-amber-500 text-white shadow-lg shadow-amber-950/30'
              : 'bg-zinc-950/60 border-zinc-800 text-zinc-400 hover:text-zinc-200'"
          >
            <div class="flex items-center justify-between mb-1">
              <span class="text-xs font-bold text-amber-400 uppercase tracking-wider">ხანგრძლივი ვადა</span>
              <span class="px-2 py-0.5 rounded text-[10px] font-mono bg-zinc-800 text-zinc-300 font-bold">6 - 36 თვე</span>
            </div>
            <div class="text-sm font-bold text-white">სტანდარტული განვადება (TBC & BOG)</div>
            <div class="text-xs text-zinc-400 mt-1">მინიმალური ყოველთვიური გადასახადი თქვენს ბიუჯეტზე.</div>
          </button>
        </div>

        <!-- 1. BOG Part-by-Part Schedule View -->
        <div v-if="activeTab === 'bog-part'" class="space-y-4">
          <div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800">
            <div class="text-xs font-bold text-zinc-400 mb-3 uppercase tracking-wider">
              გადახდის 4-თვიანი გრაფიკი (0% ზედმეტობა):
            </div>
            <div class="grid grid-cols-2 sm:grid-cols-4 gap-2.5">
              <div class="p-3 rounded-lg bg-zinc-900 border border-orange-500/40 text-center">
                <div class="text-[11px] text-orange-400 font-semibold">1. ყიდვის დღეს</div>
                <div class="text-base font-black text-white font-mono mt-0.5">{{ bogPaymentPerMonth }} ₾</div>
                <div class="text-[10px] text-zinc-500">დღესვე</div>
              </div>
              <div class="p-3 rounded-lg bg-zinc-900 border border-zinc-800 text-center">
                <div class="text-[11px] text-zinc-300 font-semibold">2. I თვეში</div>
                <div class="text-base font-black text-white font-mono mt-0.5">{{ bogPaymentPerMonth }} ₾</div>
                <div class="text-[10px] text-zinc-500">30 დღის შემდეგ</div>
              </div>
              <div class="p-3 rounded-lg bg-zinc-900 border border-zinc-800 text-center">
                <div class="text-[11px] text-zinc-300 font-semibold">3. II თვეში</div>
                <div class="text-base font-black text-white font-mono mt-0.5">{{ bogPaymentPerMonth }} ₾</div>
                <div class="text-[10px] text-zinc-500">60 დღის შემდეგ</div>
              </div>
              <div class="p-3 rounded-lg bg-zinc-900 border border-zinc-800 text-center">
                <div class="text-[11px] text-zinc-300 font-semibold">4. III თვეში</div>
                <div class="text-base font-black text-white font-mono mt-0.5">{{ bogPaymentPerMonth }} ₾</div>
                <div class="text-[10px] text-zinc-500">90 დღის შემდეგ</div>
              </div>
            </div>

            <div class="mt-4 pt-3 border-t border-zinc-800/80 flex items-center justify-between text-xs text-zinc-300">
              <span class="flex items-center gap-1.5 text-emerald-400">
                <CheckCircle2 class="w-4 h-4 text-emerald-400" />
                სულ გადასახდელი: {{ productPrice.toLocaleString() }} ₾ (ზედმეტობა 0 ₾)
              </span>
              <span class="text-zinc-500">საქართველოს ბანკი 0%</span>
            </div>
          </div>
        </div>

        <!-- 2. Standard Installment Calculator (6, 12, 18, 24, 36 months) -->
        <div v-else class="space-y-4">
          <!-- Term Selector -->
          <div>
            <label class="block text-xs font-bold text-zinc-300 uppercase tracking-wider mb-2">
              განვადების ვადა (თვე):
            </label>
            <div class="grid grid-cols-5 gap-2">
              <button
                v-for="term in terms"
                :key="term"
                @click="selectedTerm = term"
                class="py-2.5 rounded-xl text-xs font-bold border transition-all cursor-pointer text-center font-mono"
                :class="selectedTerm === term
                  ? 'bg-amber-600 border-amber-500 text-white shadow-md'
                  : 'bg-zinc-950 border-zinc-800 text-zinc-300 hover:border-zinc-700'"
              >
                {{ term }} თვე
              </button>
            </div>
          </div>

          <!-- Downpayment slider -->
          <div>
            <div class="flex items-center justify-between text-xs text-zinc-300 mb-1.5">
              <span class="font-bold uppercase tracking-wider">პირველადი შენატანი:</span>
              <span class="font-mono text-amber-400 font-bold">{{ downpaymentPercent }}% ({{ downpaymentAmount }} ₾)</span>
            </div>
            <input 
              type="range" 
              min="0" 
              max="50" 
              step="5"
              v-model.number="downpaymentPercent" 
              class="w-full accent-amber-500 cursor-pointer bg-zinc-800 h-2 rounded-lg"
            />
          </div>

          <!-- Result summary box -->
          <div class="p-4 rounded-xl bg-zinc-950 border border-zinc-800 flex items-center justify-between gap-4 flex-wrap">
            <div>
              <div class="text-xs text-zinc-400">ყოველთვიური გადასახადი:</div>
              <div class="text-2xl font-black text-amber-400 font-mono">
                ~{{ standardMonthlyPayment }} ₾ <span class="text-xs text-zinc-500 font-normal">/ თვეში</span>
              </div>
            </div>
            <div class="text-right">
              <div class="text-xs text-zinc-400">სულ გადასახდელი:</div>
              <div class="text-lg font-bold text-white font-mono">
                ~{{ totalPayableStandard.toLocaleString() }} ₾
              </div>
            </div>
          </div>
        </div>

        <!-- Quick Application Mini-Form -->
        <div class="pt-4 border-t border-zinc-800 space-y-3">
          <div class="text-xs font-bold text-zinc-300 uppercase tracking-wider">
            სწრაფი ონლაინ განაცხადი (1 წუთში):
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-3 gap-2.5">
            <input 
              v-model="applicantForm.fullName" 
              type="text" 
              placeholder="სახელი და გვარი" 
              class="bg-zinc-950 border border-zinc-800 rounded-xl px-3 py-2 text-xs text-white placeholder-zinc-500 focus:border-orange-500 outline-none"
            />
            <input 
              v-model="applicantForm.personalId" 
              type="text" 
              maxlength="11" 
              placeholder="პირადი ნომერი (11 ნიშნა)" 
              class="bg-zinc-950 border border-zinc-800 rounded-xl px-3 py-2 text-xs text-white placeholder-zinc-500 focus:border-orange-500 outline-none font-mono"
            />
            <input 
              v-model="applicantForm.phone" 
              type="tel" 
              placeholder="+995 5XX XX XX XX" 
              class="bg-zinc-950 border border-zinc-800 rounded-xl px-3 py-2 text-xs text-white placeholder-zinc-500 focus:border-orange-500 outline-none font-mono"
            />
          </div>

          <button
            @click="submitApplication"
            :disabled="isSubmitting"
            class="w-full py-3 rounded-xl bg-orange-600 hover:bg-orange-500 text-white font-bold text-sm transition-all duration-150 cursor-pointer shadow-lg shadow-orange-950/40 flex items-center justify-center gap-2"
          >
            <Sparkles class="w-4 h-4 text-amber-200" />
            <span>{{ isSubmitting ? 'იგზავნება ბანკში...' : t.applyInstallmentBtn }}</span>
          </button>

          <p class="text-[11px] text-zinc-500 text-center leading-relaxed">
            {{ t.interestRateNote }}
          </p>
        </div>

      </div>

    </div>
  </div>
</template>
