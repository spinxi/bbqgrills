<script setup>
import { ArrowRight, Flame, Truck, ShieldCheck, Sparkles, Users, Scale, Maximize2, ShoppingBag } from 'lucide-vue-next'

const props = defineProps({
  t: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['scroll-to-grid', 'scroll-to-delivery', 'add-flagship-to-cart'])
</script>

<template>
  <section class="relative min-h-[540px] lg:min-h-[600px] flex items-center bg-zinc-950 overflow-hidden border-b border-zinc-900">
    <!-- Atmospheric Real Barbecue & Charcoal Background -->
    <div class="absolute inset-0 z-0">
      <img
        src="https://images.unsplash.com/photo-1555041469-a586c61ea9bc?auto=format&fit=crop&w=1920&q=85"
        alt="BBQ Grills Georgia Charcoal Barbecue"
        class="w-full h-full object-cover object-center opacity-30 scale-105"
      />
      <!-- Dramatic Dark Gradient Overlays -->
      <div class="absolute inset-0 bg-gradient-to-r from-zinc-950 via-zinc-950/90 to-transparent"></div>
      <div class="absolute inset-0 bg-gradient-to-t from-zinc-950 via-transparent to-zinc-950/50"></div>
    </div>

    <!-- Hero Content -->
    <div class="max-w-7xl mx-auto px-4 sm:px-6 pt-10 sm:pt-14 pb-12 sm:pb-16 relative z-10 w-full">
      <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 lg:gap-12 items-center">
        
        <!-- Left Column: Copy & CTAs (7 cols) -->
        <div class="lg:col-span-7 space-y-5 sm:space-y-6">
          
          <!-- Black Friday Urgent Pill -->
          <div class="inline-flex items-center gap-2 px-3.5 py-1.5 rounded-full bg-gradient-to-r from-red-600/20 to-orange-600/20 border border-orange-500/40 text-orange-400 text-xs font-bold uppercase tracking-wider">
            <Flame class="w-4 h-4 text-orange-500 fill-orange-500 animate-pulse" />
            <span>{{ t.blackFridayBadge }}</span>
          </div>

          <!-- Bold Modern Headline -->
          <h1 class="text-3xl sm:text-4xl md:text-5xl lg:text-6xl font-black text-white tracking-tight leading-[1.12]">
            {{ t.heroHeadline }}
          </h1>

          <!-- Description -->
          <p class="text-sm sm:text-base md:text-lg text-zinc-300 leading-relaxed max-w-xl font-normal">
            {{ t.heroDescription }}
          </p>

          <!-- Core Benefits Row -->
          <div class="grid grid-cols-3 gap-2 sm:gap-4 pt-2 max-w-lg border-t border-zinc-800/80">
            <div class="flex items-center gap-2 text-xs text-zinc-300">
              <Truck class="w-4 h-4 text-orange-400 shrink-0" />
              <span>{{ t.benefitDelivery }}</span>
            </div>
            <div class="flex items-center gap-2 text-xs text-zinc-300">
              <ShieldCheck class="w-4 h-4 text-emerald-400 shrink-0" />
              <span>{{ t.benefitSteel }}</span>
            </div>
            <div class="flex items-center gap-2 text-xs text-zinc-300">
              <Sparkles class="w-4 h-4 text-yellow-400 shrink-0" />
              <span>{{ t.benefitQuality }}</span>
            </div>
          </div>

          <!-- Action Buttons (No WhatsApp, purely online shopping) -->
          <div class="pt-2 flex flex-col sm:flex-row items-stretch sm:items-center gap-3 sm:gap-4">
            <button
              @click="emit('scroll-to-grid')"
              class="px-8 py-3.5 rounded-full bg-orange-600 hover:bg-orange-500 text-white font-bold text-sm transition-all cursor-pointer shadow-xl shadow-orange-950/60 active:scale-[0.98] flex items-center justify-center gap-2 group"
            >
              <span>{{ t.ctaExplore }}</span>
              <ArrowRight class="w-4 h-4 group-hover:translate-x-1 transition-transform" />
            </button>

            <button
              @click="emit('scroll-to-delivery')"
              class="px-6 py-3.5 rounded-full bg-zinc-900/90 hover:bg-zinc-800 text-zinc-300 hover:text-white border border-zinc-800 font-medium text-sm transition-all cursor-pointer flex items-center justify-center gap-2"
            >
              <Truck class="w-4 h-4 text-zinc-400" />
              <span>{{ t.ctaDeliveryInfo }}</span>
            </button>
          </div>

        </div>

        <!-- Right Column: Featured Black Friday Promo Card (5 cols) -->
        <div class="lg:col-span-5">
          <div class="relative rounded-3xl bg-gradient-to-b from-zinc-900 via-zinc-900/95 to-zinc-950 border-2 border-orange-500/50 p-6 sm:p-7 shadow-2xl shadow-orange-950/40 space-y-5 backdrop-blur-md">
            
            <!-- Top Card Header -->
            <div class="flex items-center justify-between gap-2">
              <span class="px-3 py-1 rounded-full text-xs font-black uppercase tracking-wider bg-orange-600 text-white shadow-md">
                {{ t.flagshipBadge }}
              </span>
              <span class="text-xs font-mono font-bold text-emerald-400 flex items-center gap-1">
                ● {{ t.inStockBadge }}
              </span>
            </div>

            <!-- Model Title & Subtitle -->
            <div>
              <h3 class="text-xl sm:text-2xl font-black text-white tracking-tight">
                {{ t.flagshipTitle }}
              </h3>
              <p class="text-xs text-zinc-400 mt-1">
                {{ t.flagshipSubtitle }}
              </p>
            </div>

            <!-- 3 Key Specs Badge Box -->
            <div class="grid grid-cols-3 gap-2 p-3 rounded-2xl bg-zinc-950 border border-zinc-800 text-center">
              <div class="p-1.5">
                <div class="text-[10px] text-zinc-400 font-medium flex items-center justify-center gap-1">
                  <Maximize2 class="w-3 h-3 text-orange-400" />
                  <span>{{ t.specAreaLabel }}</span>
                </div>
                <div class="text-xs sm:text-sm font-bold text-white mt-0.5">{{ t.specAreaVal }}</div>
              </div>
              <div class="p-1.5 border-x border-zinc-800">
                <div class="text-[10px] text-zinc-400 font-medium flex items-center justify-center gap-1">
                  <Users class="w-3 h-3 text-orange-400" />
                  <span>{{ t.specCapacityLabel }}</span>
                </div>
                <div class="text-xs sm:text-sm font-bold text-white mt-0.5">{{ t.specCapacityVal }}</div>
              </div>
              <div class="p-1.5">
                <div class="text-[10px] text-zinc-400 font-medium flex items-center justify-center gap-1">
                  <Scale class="w-3 h-3 text-orange-400" />
                  <span>{{ t.specWeightLabel }}</span>
                </div>
                <div class="text-xs sm:text-sm font-bold text-white mt-0.5">{{ t.specWeightVal }}</div>
              </div>
            </div>

            <!-- Pricing Row & Direct Buy Action -->
            <div class="flex items-center justify-between pt-1">
              <div>
                <div class="text-[10px] text-zinc-400 font-medium uppercase tracking-wider">{{ t.promoPriceLabel }}</div>
                <div class="flex items-baseline gap-2">
                  <span class="text-3xl sm:text-4xl font-black text-white font-mono">549 ₾</span>
                  <span class="text-sm text-zinc-500 line-through font-mono">650 ₾</span>
                </div>
              </div>

              <!-- Direct Add to Cart Button -->
              <button
                @click="emit('add-flagship-to-cart')"
                class="px-5 py-3 rounded-2xl bg-orange-600 hover:bg-orange-500 text-white font-bold text-xs transition-all flex items-center gap-2 shadow-lg shadow-orange-950/50 cursor-pointer active:scale-[0.98]"
              >
                <ShoppingBag class="w-4 h-4" />
                <span>{{ t.addToCart }}</span>
              </button>
            </div>

            <!-- Urgency Note -->
            <div class="text-[11px] text-orange-300 text-center font-medium bg-orange-950/40 p-2 rounded-xl border border-orange-800/40">
              ⚡ {{ t.urgencyText }}
            </div>

          </div>
        </div>

      </div>
    </div>
  </section>
</template>
