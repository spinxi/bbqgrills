<script setup>
import { MapPin, Phone, Clock, Store, CheckCircle2, ExternalLink, ShieldCheck } from 'lucide-vue-next'

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

const emit = defineEmits(['filter-branch'])
</script>

<template>
  <section id="showrooms" class="py-14 sm:py-20 bg-zinc-950 border-t border-zinc-800 relative">
    <div class="max-w-7xl mx-auto px-4">
      
      <!-- Section Title -->
      <div class="text-center max-w-2xl mx-auto mb-12 space-y-2">
        <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-red-500/10 border border-red-500/30 text-red-400 text-xs font-bold uppercase tracking-wider">
          <Store class="w-3.5 h-3.5" />
          <span>Omnichannel Showrooms</span>
        </div>
        <h2 class="text-2xl sm:text-3xl md:text-4xl font-black text-white tracking-tight">
          {{ t.showroomsTitle }}
        </h2>
        <p class="text-sm text-zinc-400 leading-relaxed">
          {{ t.showroomsSubtitle }}
        </p>
      </div>

      <!-- 2 Showroom Cards -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-6 lg:gap-8">
        
        <!-- Showroom 1: Beliashvili 43 -->
        <div class="rounded-2xl bg-zinc-900/90 border border-zinc-800 hover:border-red-500/40 p-6 sm:p-8 space-y-6 transition-all duration-300 shadow-xl flex flex-col justify-between">
          <div class="space-y-4">
            <div class="flex items-start justify-between gap-3">
              <div>
                <span class="px-2.5 py-0.5 rounded text-[10px] font-mono font-bold bg-red-950 text-red-300 border border-red-800/40 uppercase">
                  {{ t.flagshipBadge }}
                </span>
                <h3 class="text-xl font-bold text-white mt-1.5 flex items-center gap-2">
                  <span>{{ t.flagshipBeliashvili }}</span>
                </h3>
                <div class="text-xs text-zinc-400 mt-0.5">{{ t.flagshipArea }}</div>
              </div>
              <div class="w-10 h-10 rounded-xl bg-red-600/10 border border-red-500/30 text-red-400 flex items-center justify-center shrink-0">
                <Store class="w-5 h-5" />
              </div>
            </div>

            <!-- Details list -->
            <div class="space-y-2.5 text-xs text-zinc-300 pt-2">
              <div class="flex items-center gap-2.5">
                <Clock class="w-4 h-4 text-red-400 shrink-0" />
                <span>{{ t.hoursBeliashvili }}</span>
              </div>
              <div class="flex items-center gap-2.5">
                <Phone class="w-4 h-4 text-red-400 shrink-0" />
                <a href="tel:+995322123456" class="hover:text-white transition-colors">+995 (32) 2 123 456</a>
              </div>
              <div class="flex items-center gap-2.5">
                <MapPin class="w-4 h-4 text-red-400 shrink-0" />
                <span>{{ t.addressBeliashvili }}</span>
              </div>
            </div>

            <!-- Live in-store stock counter -->
            <div class="p-3.5 rounded-xl bg-zinc-950 border border-zinc-800/80 flex items-center justify-between">
              <div class="text-xs text-zinc-400">
                <div>{{ t.currentLiveStock }}</div>
                <div class="text-emerald-400 font-bold font-mono text-sm mt-0.5">{{ t.stockBeliashviliLive }}</div>
              </div>
              <button
                @click="emit('filter-branch', 'beliashvili')"
                class="px-3 py-1.5 rounded-lg bg-zinc-800 hover:bg-red-600 text-zinc-200 hover:text-white text-xs font-semibold transition-all cursor-pointer"
              >
                {{ t.viewBranchStock }}
              </button>
            </div>
          </div>

          <!-- Bottom map button -->
          <a
            href="https://maps.google.com/?q=Beliashvili+43+Tbilisi"
            target="_blank"
            rel="noopener noreferrer"
            class="w-full py-2.5 rounded-xl bg-zinc-800 hover:bg-zinc-700 text-zinc-200 hover:text-white text-xs font-bold transition-all flex items-center justify-center gap-2 border border-zinc-700/60"
          >
            <span>{{ t.openInMaps }}</span>
            <ExternalLink class="w-3.5 h-3.5 text-zinc-400" />
          </a>
        </div>

        <!-- Showroom 2: City Mall Saburtalo -->
        <div class="rounded-2xl bg-zinc-900/90 border border-zinc-800 hover:border-red-500/40 p-6 sm:p-8 space-y-6 transition-all duration-300 shadow-xl flex flex-col justify-between">
          <div class="space-y-4">
            <div class="flex items-start justify-between gap-3">
              <div>
                <span class="px-2.5 py-0.5 rounded text-[10px] font-mono font-bold bg-red-950 text-red-300 border border-red-800/40 uppercase">
                  {{ t.mallBadge }}
                </span>
                <h3 class="text-xl font-bold text-white mt-1.5 flex items-center gap-2">
                  <span>{{ t.mallName }}</span>
                </h3>
                <div class="text-xs text-zinc-400 mt-0.5">{{ t.mallArea }}</div>
              </div>
              <div class="w-10 h-10 rounded-xl bg-red-600/10 border border-red-500/30 text-red-400 flex items-center justify-center shrink-0">
                <Store class="w-5 h-5" />
              </div>
            </div>

            <!-- Details list -->
            <div class="space-y-2.5 text-xs text-zinc-300 pt-2">
              <div class="flex items-center gap-2.5">
                <Clock class="w-4 h-4 text-red-400 shrink-0" />
                <span>{{ t.hoursCityMall }}</span>
              </div>
              <div class="flex items-center gap-2.5">
                <Phone class="w-4 h-4 text-red-400 shrink-0" />
                <a href="tel:+995322123457" class="hover:text-white transition-colors">+995 (32) 2 123 457</a>
              </div>
              <div class="flex items-center gap-2.5">
                <MapPin class="w-4 h-4 text-red-400 shrink-0" />
                <span>{{ t.addressCityMall }}</span>
              </div>
            </div>

            <!-- Live in-store stock counter -->
            <div class="p-3.5 rounded-xl bg-zinc-950 border border-zinc-800/80 flex items-center justify-between">
              <div class="text-xs text-zinc-400">
                <div>{{ t.currentLiveStock }}</div>
                <div class="text-emerald-400 font-bold font-mono text-sm mt-0.5">{{ t.stockCityMallLive }}</div>
              </div>
              <button
                @click="emit('filter-branch', 'citymall')"
                class="px-3 py-1.5 rounded-lg bg-zinc-800 hover:bg-red-600 text-zinc-200 hover:text-white text-xs font-semibold transition-all cursor-pointer"
              >
                {{ t.viewBranchStock }}
              </button>
            </div>
          </div>

          <!-- Bottom map button -->
          <a
            href="https://maps.google.com/?q=City+Mall+Saburtalo+Tbilisi"
            target="_blank"
            rel="noopener noreferrer"
            class="w-full py-2.5 rounded-xl bg-zinc-800 hover:bg-zinc-700 text-zinc-200 hover:text-white text-xs font-bold transition-all flex items-center justify-center gap-2 border border-zinc-700/60"
          >
            <span>{{ t.openInMaps }}</span>
            <ExternalLink class="w-3.5 h-3.5 text-zinc-400" />
          </a>
        </div>

      </div>

    </div>
  </section>
</template>
