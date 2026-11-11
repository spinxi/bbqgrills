<script setup>
import { computed } from 'vue'
import { AlertTriangle, Zap, RefreshCw, CheckCircle2, ShieldCheck } from 'lucide-vue-next'

const props = defineProps({
  currentLang: {
    type: String,
    required: true
  },
  t: {
    type: Object,
    required: true
  },
  isSyncing: {
    type: Boolean,
    default: false
  },
  lastSyncTime: {
    type: String,
    default: '1 წთ წინ'
  }
})

const emit = defineEmits(['open-audit', 'trigger-sync'])
</script>

<template>
  <aside 
    aria-label="Executive Prototype Demo Bar"
    class="sticky top-0 z-50 bg-gradient-to-r from-zinc-950 via-zinc-900 to-zinc-950 border-b border-orange-500/30 text-xs shadow-xl backdrop-blur-md"
  >
    <div class="max-w-7xl mx-auto px-4 py-2 sm:py-2.5 flex flex-wrap items-center justify-between gap-3">
      <!-- Left: Prototype Badge & Architecture -->
      <div class="flex items-center gap-2.5 flex-wrap">
        <span class="inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-full bg-orange-500/20 text-orange-400 font-bold border border-orange-500/40 text-[11px] tracking-wider uppercase animate-pulse">
          <span class="w-1.5 h-1.5 rounded-full bg-orange-500"></span>
          Executive Demo
        </span>
        <span class="text-zinc-200 font-semibold tracking-wide flex items-center gap-1.5">
          <span class="text-orange-500 font-bold">Grills.ge</span>
          <span class="text-zinc-500">•</span>
          <span>Next-Gen Laravel 11 + Vue 3 Architecture</span>
        </span>
        <span class="hidden md:inline-flex items-center gap-1 text-[11px] text-emerald-400/90 bg-emerald-950/60 border border-emerald-800/40 px-2 py-0.5 rounded">
          <CheckCircle2 class="w-3 h-3 text-emerald-400" />
          <span>ERP Sync: Active (1C / FINA)</span>
        </span>
      </div>

      <!-- Right: Action Buttons -->
      <div class="flex items-center gap-2 flex-wrap">
        <!-- Site Audit Button -->
        <button
          @click="emit('open-audit')"
          class="inline-flex items-center gap-1.5 px-3 py-1.5 rounded-lg bg-red-950/70 hover:bg-red-900/80 text-red-200 border border-red-700/60 font-medium transition-all duration-200 hover:scale-[1.02] active:scale-[0.98] shadow-sm hover:shadow-red-900/20 cursor-pointer"
          title="See current WordPress/Ecwid bugs vs new solution"
        >
          <AlertTriangle class="w-3.5 h-3.5 text-red-400 animate-bounce" />
          <span class="font-bold">{{ t.viewAuditBtn }}</span>
        </button>

        <!-- Live ERP Sync Simulator -->
        <button
          @click="emit('trigger-sync')"
          :disabled="isSyncing"
          class="inline-flex items-center gap-1.5 px-3 py-1.5 rounded-lg bg-orange-600/90 hover:bg-orange-500 text-white font-semibold transition-all duration-200 hover:scale-[1.02] active:scale-[0.98] shadow-md shadow-orange-950/40 cursor-pointer disabled:opacity-60"
        >
          <RefreshCw 
            class="w-3.5 h-3.5 text-amber-200" 
            :class="{ 'animate-spin': isSyncing }" 
          />
          <span>{{ isSyncing ? 'სინქრონიზაცია...' : t.erpSyncBtn }}</span>
        </button>
      </div>
    </div>
  </aside>
</template>
