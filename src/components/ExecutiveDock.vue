<script setup>
import { AlertCircle, RefreshCw, Layers } from 'lucide-vue-next'

const props = defineProps({
  t: {
    type: Object,
    required: true
  },
  isSyncing: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['open-audit', 'trigger-sync'])
</script>

<template>
  <aside 
    aria-label="Executive Demo Dock"
    class="fixed bottom-6 right-6 z-50 flex items-center gap-2 p-1.5 rounded-full bg-zinc-950/85 border border-zinc-800 shadow-2xl backdrop-blur-xl transition-all duration-300 hover:border-zinc-700"
  >
    <!-- Executive Demo Pill -->
    <div class="hidden sm:flex items-center gap-1.5 px-3 py-1 text-[11px] font-semibold text-zinc-400">
      <Layers class="w-3.5 h-3.5 text-orange-500" />
      <span>Demo Tools</span>
    </div>

    <!-- Technical Audit Button -->
    <button
      @click="emit('open-audit')"
      class="inline-flex items-center gap-1.5 px-3.5 py-1.5 rounded-full bg-red-950/80 hover:bg-red-900 text-red-200 border border-red-800/80 text-xs font-medium transition-all duration-150 cursor-pointer shadow-sm hover:scale-[1.02] active:scale-[0.98]"
      title="View 5 Critical Bugs vs Next-Gen Solution"
    >
      <AlertCircle class="w-3.5 h-3.5 text-red-400 shrink-0" />
      <span>{{ t.viewAuditBtn }}</span>
    </button>

    <!-- 1C / FINA ERP Sync Button -->
    <button
      @click="emit('trigger-sync')"
      :disabled="isSyncing"
      class="inline-flex items-center gap-1.5 px-3.5 py-1.5 rounded-full bg-zinc-800 hover:bg-zinc-700 text-zinc-100 border border-zinc-700 text-xs font-medium transition-all duration-150 cursor-pointer shadow-sm hover:scale-[1.02] active:scale-[0.98] disabled:opacity-60"
      title="Simulate 1C / FINA ERP Webhook Sync"
    >
      <RefreshCw 
        class="w-3.5 h-3.5 text-zinc-300"
        :class="{ 'animate-spin text-orange-400': isSyncing }" 
      />
      <span>{{ isSyncing ? 'სინქრონიზაცია...' : t.erpSyncBtn }}</span>
    </button>
  </aside>
</template>
