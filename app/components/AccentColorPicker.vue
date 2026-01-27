<script setup lang="ts">
import { useAccentColor, type AppSettings } from '~/composables/useSettings'

const { accentColors, selectedAccentColor, setAccentColor } = useAccentColor()

const popoverRef = ref<HTMLElement | null>(null)

function applyColor(id: AppSettings['accentColorId']) {
  setAccentColor(id)
  popoverRef.value?.hidePopover()
}
</script>

<template>
  <div class="flex items-center justify-between">
    <button
      v-for="color in accentColors"
      :key="color.id"
      type="button"
      role="option"
      :aria-selected="selectedAccentColor === color.id"
      :aria-label="color.name"
      class="size-6 rounded-full transition-transform duration-150 hover:scale-110 focus-ring aria-selected:(ring-2 ring-fg ring-offset-2 ring-offset-bg-subtle)"
      :style="{ backgroundColor: color.value }"
      @click="applyColor(color.id)"
    />
    <button
      type="button"
      aria-label="Clear accent color"
      class="size-6 rounded-full transition-transform duration-150 hover:scale-110 focus-ring flex items-center justify-center bg-accent-fallback"
      @click="applyColor(null)"
    >
      <span class="i-carbon-error size-4 text-bg" />
    </button>
  </div>
</template>
