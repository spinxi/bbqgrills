<script setup>
import { Phone, Mail } from 'lucide-vue-next'

const props = defineProps({
  t: {
    type: Object,
    required: true
  }
})
</script>

<template>
  <footer class="bg-zinc-950 border-t border-zinc-900 text-zinc-400 text-xs">
    
    <!-- Top Footer Bar with Brands -->
    <div class="border-b border-zinc-900/80 py-8">
      <div class="max-w-7xl mx-auto px-6 flex flex-wrap items-center justify-between gap-6">
        <div>
          <a href="#" class="flex items-center gap-3 group">
            <img 
              src="https://grills.ge/wp-content/uploads/2026/05/4070308544.png"
              alt="Grills.ge Official Logo"
              class="h-11 w-11 rounded-full object-contain ring-1 ring-white/15 bg-zinc-950 shadow-md group-hover:scale-105 transition-transform"
              @error="(e) => e.target.style.display = 'none'"
            />
            <span class="text-xl font-black text-white tracking-tight group-hover:text-zinc-200 transition-colors">
              GRILLS<span class="text-red-600">.GE</span>
            </span>
          </a>
          <p class="text-xs text-zinc-400 mt-2 max-w-sm">
            {{ t.footerAbout }}
          </p>
        </div>

        <!-- Bank & Payment Badges -->
        <div class="flex items-center gap-2 flex-wrap">
          <span class="px-2.5 py-1 rounded bg-zinc-900 border border-zinc-800 text-[11px] font-medium text-zinc-300">
            Visa / Mastercard
          </span>
          <span class="px-2.5 py-1 rounded bg-zinc-900 border border-zinc-800 text-[11px] font-medium text-zinc-300">
            Apple Pay
          </span>
          <span class="px-2.5 py-1 rounded bg-zinc-900 border border-zinc-800 text-[11px] font-medium text-zinc-300">
            Bank of Georgia (BOG)
          </span>
          <span class="px-2.5 py-1 rounded bg-zinc-900 border border-zinc-800 text-[11px] font-medium text-zinc-300">
            TBC Bank
          </span>
        </div>
      </div>
    </div>

    <!-- Main Footer Links Grid -->
    <div class="max-w-7xl mx-auto px-6 py-12 grid grid-cols-2 sm:grid-cols-4 gap-8">
      
      <!-- Col 1: Showrooms -->
      <div class="space-y-3">
        <h4 class="font-bold text-white uppercase tracking-wider text-[11px]">{{ t.footerShowrooms }}</h4>
        <ul class="space-y-2 text-zinc-400">
          <li>
            <strong class="text-zinc-200 block">{{ t.branchBeliashviliShort }}</strong>
            <span>{{ t.footerBeliashviliSubtitle }}</span>
          </li>
          <li class="pt-1">
            <strong class="text-zinc-200 block">{{ t.branchCityMallShort }}</strong>
            <span>{{ t.footerCityMallSubtitle }}</span>
          </li>
        </ul>
      </div>

      <!-- Col 2: Customer Service -->
      <div class="space-y-3">
        <h4 class="font-bold text-white uppercase tracking-wider text-[11px]">{{ t.customerService }}</h4>
        <ul class="space-y-2">
          <li><a href="#" class="hover:text-white transition-colors">{{ t.warrantyService }}</a></li>
          <li><a href="#" class="hover:text-white transition-colors">{{ t.deliveryTerms }}</a></li>
          <li><a href="#" class="hover:text-white transition-colors">{{ t.bbqMasterclass }}</a></li>
          <li><a href="#" class="hover:text-white transition-colors">{{ t.corporateSales }}</a></li>
        </ul>
      </div>

      <!-- Col 3: Official Brands -->
      <div class="space-y-3">
        <h4 class="font-bold text-white uppercase tracking-wider text-[11px]">{{ t.footerBrands }}</h4>
        <ul class="space-y-2">
          <li><span class="text-zinc-300">Weber® Grills USA</span></li>
          <li><span class="text-zinc-300">Big Green Egg® Kamado</span></li>
          <li><span class="text-zinc-300">Ooni® Pizza Ovens</span></li>
          <li><span class="text-zinc-300">Lodge® Cast Iron 1896</span></li>
          <li><span class="text-zinc-300">Joseph Joseph® UK</span></li>
        </ul>
      </div>

      <!-- Col 4: Contacts -->
      <div class="space-y-3">
        <h4 class="font-bold text-white uppercase tracking-wider text-[11px]">{{ t.footerContact }}</h4>
        <div class="space-y-2">
          <div class="flex items-center gap-2">
            <Phone class="w-3.5 h-3.5 text-zinc-400" />
            <a href="tel:+995322123456" class="hover:text-white transition-colors">+995 (32) 2 123 456</a>
          </div>
          <div class="flex items-center gap-2">
            <Mail class="w-3.5 h-3.5 text-zinc-400" />
            <a href="mailto:info@grills.ge" class="hover:text-white transition-colors">info@grills.ge</a>
          </div>
        </div>
      </div>

    </div>

    <!-- Bottom Copyright Ribbon -->
    <div class="border-t border-zinc-900 py-6 bg-black">
      <div class="max-w-7xl mx-auto px-6 flex flex-wrap items-center justify-between gap-3 text-[11px] text-zinc-500">
        <div>
          © {{ new Date().getFullYear() }} Grills.ge — {{ t.allRightsReserved }}
        </div>
        <div class="text-zinc-500">
          {{ t.footerTagline }}
        </div>
      </div>
    </div>

  </footer>
</template>
