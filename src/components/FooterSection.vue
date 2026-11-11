<script setup>
import { Phone, Truck, Flame, CreditCard, MessageCircle, MapPin } from 'lucide-vue-next'

const props = defineProps({
  t: {
    type: Object,
    required: true
  }
})

const hashtags = [
  '#BBQGrillsGeorgia',
  '#Grill',
  '#Grills',
  '#Grilling',
  '#OutdoorCooking',
  '#ნახშირისგრილი',
  '#გრილი',
  '#მაყალი',
  '#ჯანსაღიკვება',
  '#ცეცხლზემომზადებულისაჭმელი'
]
</script>

<template>
  <footer class="bg-zinc-950 border-t border-zinc-900 text-zinc-400 text-xs">
    
    <!-- Top Footer Bar -->
    <div class="border-b border-zinc-900/80 py-8">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 flex flex-wrap items-center justify-between gap-6">
        <div>
          <a href="#" class="flex items-center gap-2.5 group">
            <div class="h-9 w-9 rounded-xl bg-gradient-to-br from-orange-600 to-red-700 flex items-center justify-center text-white shadow-md">
              <Flame class="w-5 h-5 text-white" />
            </div>
            <span class="text-xl font-black text-white tracking-tight group-hover:text-zinc-200 transition-colors">
              BBQ GRILLS <span class="text-orange-500 font-extrabold text-sm px-1.5 py-0.5 rounded bg-orange-950/60 border border-orange-800/60">GEORGIA</span>
            </span>
          </a>
          <p class="text-xs text-zinc-400 mt-2 max-w-md leading-relaxed">
            {{ t.footerAbout }}
          </p>
        </div>

        <!-- Support Actions: WhatsApp & Phone -->
        <div class="flex items-center gap-3 flex-wrap">
          <a
            href="https://wa.me/995511195755"
            target="_blank"
            rel="noopener noreferrer"
            class="inline-flex items-center gap-2 px-4 py-2 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-white font-bold transition-all shadow-lg shadow-emerald-950/40 cursor-pointer"
          >
            <MessageCircle class="w-4 h-4" />
            <span>{{ t.footerWriteWhatsApp }}</span>
          </a>

          <a
            href="tel:+995511195755"
            class="inline-flex items-center gap-2 px-4 py-2 rounded-xl bg-zinc-900 hover:bg-zinc-800 border border-zinc-800 text-white font-bold transition-all cursor-pointer"
          >
            <Phone class="w-4 h-4 text-orange-400" />
            <span>+995 511 19 57 55</span>
          </a>
        </div>
      </div>
    </div>

    <!-- Main Footer Grid -->
    <div class="max-w-7xl mx-auto px-4 sm:px-6 py-10 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-8">
      
      <!-- Col 1: Delivery Terms -->
      <div class="space-y-3">
        <h4 class="font-bold text-white uppercase tracking-wider text-[11px] flex items-center gap-1.5">
          <Truck class="w-4 h-4 text-orange-400" />
          <span>{{ t.footerDeliveryTerms }}</span>
        </h4>
        <ul class="space-y-2 text-zinc-400 text-xs">
          <li>
            <strong class="text-zinc-200 block">{{ t.tariffPickupTitle }}</strong>
            <span>{{ t.footerPickupNote }}</span>
          </li>
          <li class="pt-1">
            <strong class="text-zinc-200 block">{{ t.tariffTbilisiTitle }}</strong>
            <span>{{ t.footerTbilisiNote }}</span>
          </li>
          <li class="pt-1">
            <strong class="text-zinc-200 block">{{ t.tariffRegionsTitle }}</strong>
            <span>{{ t.footerRegionsNote }}</span>
          </li>
        </ul>
      </div>

      <!-- Col 2: Online Payment & Customer Support (NO TBC transfer) -->
      <div class="space-y-3">
        <h4 class="font-bold text-white uppercase tracking-wider text-[11px] flex items-center gap-1.5">
          <CreditCard class="w-4 h-4 text-cyan-400" />
          <span>{{ t.footerPaymentMethods }}</span>
        </h4>
        <p class="text-xs text-zinc-400 leading-relaxed">
          {{ t.footerOnlinePayDesc }}
        </p>
        <div class="flex items-center gap-1.5 flex-wrap pt-1">
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-[10px] text-zinc-300">Visa</span>
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-[10px] text-zinc-300">Mastercard</span>
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-[10px] text-zinc-300">Apple Pay</span>
          <span class="px-2 py-0.5 rounded bg-zinc-900 border border-zinc-800 text-[10px] text-zinc-300">Google Pay</span>
        </div>
        <div class="pt-2 text-xs text-zinc-400 flex items-center gap-1.5">
          <MapPin class="w-3.5 h-3.5 text-orange-400 shrink-0" />
          <span>{{ t.footerLocation }}</span>
        </div>
      </div>

      <!-- Col 3: Hashtags & Community -->
      <div class="sm:col-span-2 space-y-3">
        <h4 class="font-bold text-white uppercase tracking-wider text-[11px]">{{ t.hashtagsHeading }}</h4>
        <div class="flex flex-wrap gap-1.5">
          <span 
            v-for="tag in hashtags" 
            :key="tag"
            class="px-2.5 py-1 rounded-lg bg-zinc-900 border border-zinc-800 text-[11px] text-zinc-400 hover:text-white hover:border-zinc-700 transition-colors"
          >
            {{ tag }}
          </span>
        </div>
      </div>

    </div>

    <!-- Bottom Copyright -->
    <div class="border-t border-zinc-900 py-6 text-center text-zinc-600 text-[11px]">
      <p>© {{ new Date().getFullYear() }} BBQ Grills Georgia. {{ t.allRightsReserved }}</p>
    </div>

  </footer>
</template>
