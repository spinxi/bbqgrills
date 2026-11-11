<script setup>
import { 
  Truck, 
  CreditCard, 
  Clock, 
  ShieldCheck,
  PackageCheck,
  Lock,
  Banknote
} from 'lucide-vue-next'

const props = defineProps({
  t: {
    type: Object,
    required: true
  },
  currentLang: {
    type: String,
    required: true
  }
})
</script>

<template>
  <section id="delivery-section" class="py-14 sm:py-20 bg-zinc-950 border-t border-zinc-800 relative">
    <div class="max-w-7xl mx-auto px-4 sm:px-6">
      
      <!-- Section Title -->
      <div class="text-center max-w-2xl mx-auto mb-12 space-y-3">
        <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-orange-500/10 border border-orange-500/30 text-orange-400 text-xs font-bold uppercase tracking-wider">
          <Truck class="w-3.5 h-3.5" />
          <span>{{ t.deliverySectionTag }}</span>
        </div>
        <h2 class="text-2xl sm:text-3xl md:text-4xl font-black text-white tracking-tight">
          {{ t.deliverySectionTitle }}
        </h2>
        <p class="text-xs sm:text-sm text-zinc-400 leading-relaxed">
          {{ t.deliverySectionSubtitle }}
        </p>
      </div>

      <!-- 3 Delivery Cards -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4 sm:gap-6 mb-10">
        
        <!-- Tariff 1: Pickup -->
        <div class="rounded-3xl bg-zinc-900/80 border border-zinc-800 p-6 space-y-4 hover:border-zinc-700 transition-all flex flex-col justify-between">
          <div class="space-y-3">
            <div class="w-10 h-10 rounded-2xl bg-zinc-800 text-zinc-300 flex items-center justify-center">
              <PackageCheck class="w-5 h-5 text-emerald-400" />
            </div>
            <div>
              <h3 class="text-base font-bold text-white">{{ t.tariffPickupTitle }}</h3>
              <p class="text-xs text-zinc-400 mt-1">{{ t.tariffPickupDesc }}</p>
            </div>
          </div>
          <div class="pt-3 border-t border-zinc-800 flex items-baseline justify-between">
            <span class="text-xs text-zinc-500 font-medium">{{ t.tariffPriceLabel }}</span>
            <span class="text-xl font-black text-emerald-400 font-mono">{{ t.tariffPickupPrice }}</span>
          </div>
        </div>

        <!-- Tariff 2: Tbilisi Courier -->
        <div class="rounded-3xl bg-zinc-900/80 border-2 border-orange-500/40 p-6 space-y-4 shadow-xl shadow-orange-950/20 flex flex-col justify-between">
          <div class="space-y-3">
            <div class="flex items-center justify-between">
              <div class="w-10 h-10 rounded-2xl bg-orange-600/15 text-orange-400 flex items-center justify-center">
                <Truck class="w-5 h-5" />
              </div>
              <span class="text-[10px] font-mono font-bold uppercase tracking-wider px-2 py-0.5 rounded bg-orange-950 text-orange-300 border border-orange-800/40">
                {{ t.expressTag }}
              </span>
            </div>
            <div>
              <h3 class="text-base font-bold text-white">{{ t.tariffTbilisiTitle }}</h3>
              <div class="text-xs text-zinc-400 flex items-center gap-1.5 mt-1">
                <Clock class="w-3.5 h-3.5 text-orange-400" />
                <span>{{ t.tariffTbilisiTime }}</span>
              </div>
            </div>
          </div>
          <div class="pt-3 border-t border-zinc-800 flex items-baseline justify-between">
            <span class="text-xs text-zinc-500 font-medium">{{ t.courierToDoor }}</span>
            <span class="text-2xl font-black text-white font-mono">{{ t.tariffTbilisiPrice }}</span>
          </div>
        </div>

        <!-- Tariff 3: Regions -->
        <div class="rounded-3xl bg-zinc-900/80 border border-zinc-800 p-6 space-y-4 hover:border-zinc-700 transition-all flex flex-col justify-between">
          <div class="space-y-3">
            <div class="w-10 h-10 rounded-2xl bg-zinc-800 text-zinc-300 flex items-center justify-center">
              <Truck class="w-5 h-5 text-cyan-400" />
            </div>
            <div>
              <h3 class="text-base font-bold text-white">{{ t.tariffRegionsTitle }}</h3>
              <div class="text-xs text-zinc-400 flex items-center gap-1.5 mt-1">
                <Clock class="w-3.5 h-3.5 text-cyan-400" />
                <span>{{ t.tariffRegionsTime }}</span>
              </div>
            </div>
          </div>
          <div class="pt-3 border-t border-zinc-800 flex items-baseline justify-between">
            <span class="text-xs text-zinc-500 font-medium">{{ t.courierDelivery }}</span>
            <span class="text-2xl font-black text-white font-mono">{{ t.tariffRegionsPrice }}</span>
          </div>
        </div>

      </div>

      <!-- Payment & Security Box (Online Payment & COD - No TBC transfer) -->
      <div class="rounded-3xl bg-zinc-900/90 border border-zinc-800 p-6 sm:p-8 grid grid-cols-1 md:grid-cols-2 gap-6 items-center">
        
        <div class="space-y-3">
          <div class="inline-flex items-center gap-1.5 text-xs font-bold text-cyan-400 uppercase tracking-wider">
            <CreditCard class="w-4 h-4" />
            <span>{{ t.paymentHeading }}</span>
          </div>
          <h3 class="text-xl font-bold text-white">{{ t.paymentTitle }}</h3>
          <p class="text-xs text-zinc-400 leading-relaxed">
            {{ t.paymentDesc }}
          </p>
          <div class="flex items-center gap-2 pt-1 text-xs text-zinc-300">
            <ShieldCheck class="w-4 h-4 text-emerald-400 shrink-0" />
            <span>{{ t.qualityCheckNote }}</span>
          </div>
        </div>

        <!-- Payment Badges & Security Card -->
        <div class="p-5 rounded-2xl bg-zinc-950 border border-zinc-800 space-y-4">
          <div class="flex items-center gap-2 text-xs font-bold text-white">
            <Lock class="w-4 h-4 text-emerald-400" />
            <span>{{ t.paymentOnlineBadge }}</span>
          </div>
          
          <div class="flex items-center gap-2 flex-wrap">
            <span class="px-3 py-1.5 rounded-xl bg-zinc-900 border border-zinc-800 text-xs font-semibold text-zinc-200">
              💳 Visa
            </span>
            <span class="px-3 py-1.5 rounded-xl bg-zinc-900 border border-zinc-800 text-xs font-semibold text-zinc-200">
              💳 Mastercard
            </span>
            <span class="px-3 py-1.5 rounded-xl bg-zinc-900 border border-zinc-800 text-xs font-semibold text-zinc-200">
               Apple Pay
            </span>
            <span class="px-3 py-1.5 rounded-xl bg-zinc-900 border border-zinc-800 text-xs font-semibold text-zinc-200">
              G Pay
            </span>
          </div>

          <div class="pt-2 border-t border-zinc-800/80 flex items-center gap-2 text-xs text-zinc-400">
            <Banknote class="w-4 h-4 text-emerald-400" />
            <span>{{ t.paymentCodBadge }}</span>
          </div>
        </div>

      </div>

    </div>
  </section>
</template>
