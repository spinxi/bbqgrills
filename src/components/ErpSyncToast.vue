<script setup>
import { CheckCircle2, Zap, X, Database } from 'lucide-vue-next'

const props = defineProps({
  isVisible: {
    type: Boolean,
    default: false
  },
  toastMessage: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['close'])
</script>

<template>
  <div 
    v-if="isVisible"
    class="fixed bottom-6 right-6 z-50 max-w-md w-full p-4 rounded-2xl bg-zinc-900 border border-emerald-500/50 shadow-2xl shadow-emerald-950/40 text-white backdrop-blur-md animate-in slide-in-from-bottom-5 duration-300"
  >
    <div class="flex items-start gap-3">
      <div class="w-9 h-9 rounded-xl bg-emerald-500/20 text-emerald-400 border border-emerald-500/40 flex items-center justify-center shrink-0">
        <Zap class="w-5 h-5 animate-pulse" />
      </div>

      <div class="flex-1 min-w-0">
        <div class="flex items-center gap-1.5 text-xs font-bold text-emerald-400 uppercase tracking-wider mb-1">
          <Database class="w-3.5 h-3.5" />
          <span>FINA / 1C ERP Webhook Success</span>
        </div>
        <p class="text-xs text-zinc-200 leading-relaxed font-mono">
          {{ toastMessage }}
        </p>
        <div class="text-[10px] text-zinc-400 mt-1.5 flex items-center gap-2">
          <span>Status: 200 OK</span>
          <span>•</span>
          <span>Beliashvili: 194 SKUs</span>
          <span>•</span>
          <span>City Mall: 234 SKUs</span>
        </div>
      </div>

      <button 
        @click="emit('close')"
        class="text-zinc-400 hover:text-white p-1 rounded-lg hover:bg-zinc-800 transition-colors cursor-pointer"
      >
        <X class="w-4 h-4" />
      </button>
    </div>
  </div>
</template>
