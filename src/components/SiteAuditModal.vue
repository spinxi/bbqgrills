<script setup>
import { X, AlertTriangle, CheckCircle2, TrendingUp, Zap, Database, Smartphone, ShieldCheck, ArrowRight } from 'lucide-vue-next'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },
  t: {
    type: Object,
    required: true
  },
  currentLang: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['close'])
</script>

<template>
  <div 
    v-if="isOpen"
    class="fixed inset-0 z-50 overflow-y-auto bg-black/85 backdrop-blur-md flex items-center justify-center p-3 sm:p-6"
    @click.self="emit('close')"
  >
    <div class="relative w-full max-w-5xl bg-zinc-900 border border-zinc-700/80 rounded-2xl shadow-2xl overflow-hidden my-auto animate-in fade-in zoom-in-95 duration-200">
      
      <!-- Modal Header -->
      <div class="sticky top-0 z-10 bg-zinc-950/95 border-b border-zinc-800 px-6 py-5 flex items-start justify-between gap-4 backdrop-blur-md">
        <div>
          <div class="flex items-center gap-2 mb-1.5">
            <span class="inline-flex items-center gap-1 px-2.5 py-0.5 rounded-full bg-red-500/20 text-red-400 font-semibold border border-red-500/30 text-xs uppercase tracking-wider">
              <AlertTriangle class="w-3.5 h-3.5" />
              Executive Audit
            </span>
            <span class="text-xs text-zinc-400">Grills.ge WordPress/Ecwid vs Laravel + Vue 3</span>
          </div>
          <h2 class="text-xl sm:text-2xl font-bold text-white tracking-tight">
            {{ t.auditModalTitle }}
          </h2>
          <p class="text-sm text-zinc-400 mt-1">
            {{ t.auditModalSubtitle }}
          </p>
        </div>

        <button 
          @click="emit('close')"
          class="p-2 rounded-xl bg-zinc-800/80 hover:bg-zinc-700 text-zinc-400 hover:text-white transition-colors cursor-pointer"
          aria-label="Close modal"
        >
          <X class="w-5 h-5" />
        </button>
      </div>

      <!-- ROI Impact Metric Bar -->
      <div class="bg-gradient-to-r from-orange-950/40 via-zinc-900 to-emerald-950/40 border-b border-zinc-800/80 px-6 py-4 grid grid-cols-2 sm:grid-cols-4 gap-3 text-center">
        <div class="p-2.5 rounded-xl bg-zinc-950/60 border border-zinc-800">
          <div class="text-xs text-zinc-400">სისწრაფე (PageSpeed)</div>
          <div class="text-lg font-black text-emerald-400">0.2 წმ <span class="text-xs text-zinc-500 font-normal line-through">3.8 წმ</span></div>
        </div>
        <div class="p-2.5 rounded-xl bg-zinc-950/60 border border-zinc-800">
          <div class="text-xs text-zinc-400">კონვერსიის ზრდა</div>
          <div class="text-lg font-black text-orange-400">+35% <span class="text-xs text-zinc-400 font-normal">გაყიდვები</span></div>
        </div>
        <div class="p-2.5 rounded-xl bg-zinc-950/60 border border-zinc-800">
          <div class="text-xs text-zinc-400">მარაგის ცდომილება</div>
          <div class="text-lg font-black text-emerald-400">0% <span class="text-xs text-zinc-500 font-normal line-through">-32 მარაგი</span></div>
        </div>
        <div class="p-2.5 rounded-xl bg-zinc-950/60 border border-zinc-800">
          <div class="text-xs text-zinc-400">საბანკო განვადება</div>
          <div class="text-lg font-black text-amber-400">1-დაწკაპუნებით <span class="text-xs text-zinc-400 font-normal">BOG 0%</span></div>
        </div>
      </div>

      <!-- Bugs Comparison List -->
      <div class="p-6 space-y-5 max-h-[65vh] overflow-y-auto">
        <div 
          v-for="bug in t.auditBugs" 
          :key="bug.num"
          class="rounded-xl border border-zinc-800 bg-zinc-950/60 overflow-hidden shadow-lg transition-all hover:border-zinc-700"
        >
          <!-- Bug Number & Title -->
          <div class="px-5 py-3 bg-zinc-900/90 border-b border-zinc-800 flex items-center justify-between gap-3">
            <div class="flex items-center gap-3">
              <span class="w-6 h-6 rounded-full bg-orange-600/20 text-orange-400 border border-orange-500/40 text-xs font-black flex items-center justify-center">
                {{ bug.num }}
              </span>
              <span class="font-bold text-zinc-100 text-sm sm:text-base">
                {{ bug.titleKa }}
              </span>
            </div>
            <span class="text-[11px] font-mono px-2 py-0.5 rounded bg-zinc-800 text-zinc-400 hidden sm:inline-block">
              Priority: High
            </span>
          </div>

          <!-- Comparison Grid (Current Bug vs Solution) -->
          <div class="grid grid-cols-1 md:grid-cols-2 divide-y md:divide-y-0 md:divide-x divide-zinc-800">
            <!-- Left: Current WordPress Problem -->
            <div class="p-5 bg-red-950/15">
              <div class="flex items-center gap-2 text-xs font-bold text-red-400 mb-2">
                <AlertTriangle class="w-4 h-4 text-red-400 shrink-0" />
                <span>{{ t.currentBug }}</span>
              </div>
              <p class="text-sm text-zinc-300 leading-relaxed mb-3">
                {{ bug.descKa }}
              </p>
              
              <!-- Visual bad state badges -->
              <div v-if="bug.num === '1'" class="inline-flex items-center gap-2 p-2 rounded bg-black/60 border border-red-800/60 text-xs font-mono text-red-400">
                <span>WordPress Stock:</span>
                <span class="px-1.5 py-0.5 bg-red-500/20 text-red-300 rounded font-bold">Available: -9</span>
                <span class="px-1.5 py-0.5 bg-red-500/20 text-red-300 rounded font-bold">Available: -32</span>
              </div>

              <div v-if="bug.num === '2'" class="inline-flex items-center gap-2 p-2 rounded bg-black/60 border border-red-800/60 text-xs font-mono text-red-400">
                <span class="text-zinc-400">Raw Error:</span>
                <span class="italic text-red-300">"Please select a time for layout"</span>
              </div>

              <div v-if="bug.num === '4'" class="inline-flex items-center gap-2 p-2 rounded bg-black/60 border border-red-800/60 text-xs font-mono text-red-400">
                <span class="text-zinc-400">Leaked Widget ID:</span>
                <span class="text-red-300 font-bold">Ecwid ID: 130630053</span>
              </div>
            </div>

            <!-- Right: New Architecture Fix -->
            <div class="p-5 bg-emerald-950/15">
              <div class="flex items-center gap-2 text-xs font-bold text-emerald-400 mb-2">
                <CheckCircle2 class="w-4 h-4 text-emerald-400 shrink-0" />
                <span>{{ bug.solTitleKa }}</span>
              </div>
              <p class="text-sm text-zinc-300 leading-relaxed mb-3">
                {{ bug.solDescKa }}
              </p>

              <!-- Positive solution badge -->
              <div v-if="bug.num === '1'" class="inline-flex items-center gap-2 p-2 rounded bg-black/60 border border-emerald-800/60 text-xs font-mono text-emerald-400">
                <span>Webhook Result:</span>
                <span class="px-1.5 py-0.5 bg-emerald-500/20 text-emerald-300 rounded font-bold">Stock Clamped >= 0 + SMS Lead Capture</span>
              </div>

              <div v-if="bug.num === '3'" class="inline-flex items-center gap-2 p-2 rounded bg-black/60 border border-emerald-800/60 text-xs font-mono text-emerald-400">
                <span class="text-zinc-400">Algorithm:</span>
                <span class="text-emerald-300 font-bold">In-Stock Priority Sorting & Hide Zero-Stock Toggle</span>
              </div>

              <div v-if="bug.num === '5'" class="inline-flex items-center gap-2 p-2 rounded bg-black/60 border border-emerald-800/60 text-xs font-mono text-emerald-400">
                <span class="text-zinc-400">Feature:</span>
                <span class="text-emerald-300 font-bold">Beliashvili (3) • City Mall (1) + 4x 0% BOG</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Footer CTA -->
      <div class="bg-zinc-950 px-6 py-4 border-t border-zinc-800 flex flex-wrap items-center justify-between gap-4">
        <div class="text-xs text-zinc-400 flex items-center gap-2">
          <ShieldCheck class="w-4 h-4 text-orange-500" />
          <span>შექმნილია Grills.ge-ს ბიზნესის ზრდისთვის</span>
        </div>
        <button
          @click="emit('close')"
          class="px-5 py-2.5 rounded-xl bg-orange-600 hover:bg-orange-500 text-white font-bold text-sm transition-all duration-150 cursor-pointer shadow-md shadow-orange-950/50"
        >
          {{ t.closeModal }} & დემოს გაგრძელება
        </button>
      </div>

    </div>
  </div>
</template>
