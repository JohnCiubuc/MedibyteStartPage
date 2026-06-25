<template>
  <Transition name="toast">
    <div v-if="visible" class="toast" :class="type">
      <i class="ti" :class="iconClass" aria-hidden="true"></i>
      <span>{{ message }}</span>
    </div>
  </Transition>
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  message: String,
  type: { type: String, default: 'info' },
  duration: { type: Number, default: 3000 }
})

const emit = defineEmits(['close'])
const visible = ref(false)

const iconClass = {
  success: 'ti-circle-check',
  error: 'ti-alert-circle',
  info: 'ti-info-circle'
}[props.type]

watch(() => props.message, (val) => {
  if (val) {
    visible.value = true
    setTimeout(() => {
      visible.value = false
      setTimeout(() => emit('close'), 300)
    }, props.duration)
  }
}, { immediate: true })
</script>

<style scoped>
.toast {
  position: fixed;
  top: 24px;
  right: 24px;
  background: var(--bg-surface);
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 12px 16px;
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 12px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  box-shadow: 0 4px 12px rgba(0,0,0,.15);
  z-index: 9999;
}
.toast.success { border-color: var(--copy-ok); color: var(--copy-ok); }
.toast.error { border-color: #d47474; color: #d47474; }
.toast.info { border-color: var(--accent); color: var(--accent); }
.toast i { font-size: 16px; }
.toast-enter-active, .toast-leave-active { transition: all .3s; }
.toast-enter-from { transform: translateX(400px); opacity: 0; }
.toast-leave-to { transform: translateY(-20px); opacity: 0; }
</style>
