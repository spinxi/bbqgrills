<script setup>
import { ShieldCheck, Store, CreditCard, Truck, Award } from 'lucide-vue-next'

const props = defineProps({
  t: {
    type: Object,
    required: true
  }
})
</script>

<template>
  <section class="py-16 sm:py-24 bg-zinc-950 border-t border-zinc-900 relative">
    <div class="max-w-7xl mx-auto px-6">
      
      <!-- Section Title -->
      <div class="text-center max-w-2xl mx-auto mb-14 space-y-2">
        <div class="text-xs font-semibold uppercase tracking-[0.2em] text-red-500">
          {{ t.whyUsOfficialTag }}
        </div>
        <h2 class="text-2xl sm:text-3xl md:text-4xl font-bold text-white tracking-tight">
          {{ t.whyUsTitle }}
        </h2>
        <p class="text-sm text-zinc-400">
          {{ t.whyUsSubtitle }}
        </p>
      </div>

      <!-- 4 Customer-Facing Pillars Grid (No ERP or Developer jargon) -->
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
        
        <!-- 1. Official Warranty -->
        <div class="p-6 rounded-2xl bg-zinc-900/40 border border-zinc-800/80 hover:border-zinc-700 transition-all duration-300 space-y-3">
          <div class="w-10 h-10 rounded-xl bg-zinc-800/80 text-zinc-200 flex items-center justify-center">
            <ShieldCheck class="w-5 h-5 text-emerald-400" />
          </div>
          <h3 class="text-base font-semibold text-white">{{ t.why1Title }}</h3>
          <p class="text-xs text-zinc-400 leading-relaxed">{{ t.why1Desc }}</p>
        </div>

        <!-- 2. Official Showrooms (Customer value replacing ERP jargon) -->
        <div class="p-6 rounded-2xl bg-zinc-900/40 border border-zinc-800/80 hover:border-zinc-700 transition-all duration-300 space-y-3">
          <div class="w-10 h-10 rounded-xl bg-zinc-800/80 text-zinc-200 flex items-center justify-center">
            <Store class="w-5 h-5 text-red-500" />
          </div>
          <h3 class="text-base font-semibold text-white">{{ t.why2Title }}</h3>
          <p class="text-xs text-zinc-400 leading-relaxed">{{ t.why2Desc }}</p>
        </div>

        <!-- 3. 0% Installment -->
        <div class="p-6 rounded-2xl bg-zinc-900/40 border border-zinc-800/80 hover:border-zinc-700 transition-all duration-300 space-y-3">
          <div class="w-10 h-10 rounded-xl bg-zinc-800/80 text-zinc-200 flex items-center justify-center">
            <CreditCard class="w-5 h-5 text-red-400" />
          </div>
          <h3 class="text-base font-semibold text-white">{{ t.why3Title }}</h3>
          <p class="text-xs text-zinc-400 leading-relaxed">{{ t.why3Desc }}</p>
        </div>

        <!-- 4. Delivery & Assembly -->
        <div class="p-6 rounded-2xl bg-zinc-900/40 border border-zinc-800/80 hover:border-zinc-700 transition-all duration-300 space-y-3">
          <div class="w-10 h-10 rounded-xl bg-zinc-800/80 text-zinc-200 flex items-center justify-center">
            <Truck class="w-5 h-5 text-cyan-400" />
          </div>
          <h3 class="text-base font-semibold text-white">{{ t.why4Title }}</h3>
          <p class="text-xs text-zinc-400 leading-relaxed">{{ t.why4Desc }}</p>
        </div>

      </div>

    </div>
  </section>
</template>
