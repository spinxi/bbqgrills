<script setup>
import { ArrowRight, MapPin } from 'lucide-vue-next'

const props = defineProps({
  t: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['scroll-to-grid', 'scroll-to-showrooms'])
</script>

<template>
  <section class="relative min-h-[480px] lg:min-h-[520px] flex items-center bg-zinc-950 overflow-hidden border-b border-zinc-900">
    <!-- Atmospheric Real Charcoal Grill Background -->
    <div class="absolute inset-0 z-0">
      <img
        src="https://images.unsplash.com/photo-1529193591184-b1d58069ecdd?auto=format&fit=crop&w=1800&q=85"
        alt="Grills.ge Official Premium Barbecue"
        class="w-full h-full object-cover object-center opacity-40 scale-105"
      />
      <!-- Dramatic Dark Gradient Overlays -->
      <div class="absolute inset-0 bg-gradient-to-r from-zinc-950 via-zinc-950/85 to-transparent"></div>
      <div class="absolute inset-0 bg-gradient-to-t from-zinc-950 via-transparent to-zinc-950/40"></div>
    </div>

    <!-- Hero Content -->
    <div class="max-w-7xl mx-auto px-4 sm:px-6 pt-12 sm:pt-20 pb-12 sm:pb-20 relative z-10 w-full">
      <div class="max-w-2xl space-y-5 sm:space-y-6">
        
        <!-- Clean Subtitle Tracking Brands -->
        <div class="text-[11px] sm:text-xs font-semibold uppercase tracking-[0.2em] sm:tracking-[0.25em] text-red-500">
          {{ t.heroSubtitleBrands }}
        </div>

        <!-- Bold Modern Headline -->
        <h1 class="text-2xl sm:text-4xl md:text-5xl lg:text-6xl font-bold text-white tracking-tight leading-[1.15]">
          {{ t.heroHeadline }}
        </h1>

        <!-- Short 1-sentence subtitle -->
        <p class="text-sm sm:text-base md:text-lg text-zinc-300 leading-relaxed font-normal">
          {{ t.heroDescription }}
        </p>

        <!-- Clean CTAs: Solid White & Glassmorphism -->
        <div class="pt-2 sm:pt-4 flex flex-col sm:flex-row items-stretch sm:items-center gap-3 sm:gap-4">
          <button
            @click="emit('scroll-to-grid')"
            class="w-full sm:w-auto px-7 py-3.5 rounded-full bg-white text-zinc-950 font-bold text-sm hover:bg-zinc-200 transition-all cursor-pointer shadow-lg active:scale-[0.98] flex items-center justify-center gap-2 group"
          >
            <span>{{ t.ctaExplore }}</span>
            <ArrowRight class="w-4 h-4 group-hover:translate-x-0.5 transition-transform" />
          </button>

          <button
            @click="emit('scroll-to-showrooms')"
            class="w-full sm:w-auto px-7 py-3.5 rounded-full bg-white/5 hover:bg-white/10 text-zinc-200 hover:text-white border border-white/15 backdrop-blur-md font-medium text-sm transition-all cursor-pointer active:scale-[0.98] flex items-center justify-center gap-2"
          >
            <MapPin class="w-4 h-4 text-zinc-400" />
            <span>{{ t.ctaShowrooms }}</span>
          </button>
        </div>

      </div>
    </div>
  </section>
</template>
