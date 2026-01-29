<script setup lang="ts">
const props = defineProps<{
  optionalDependencies: Record<string, string>
}>()

// Expanded state
const optionalDepsExpanded = shallowRef(false)

// Sort optional dependencies alphabetically
const sortedOptionalDependencies = computed(() => {
  if (!props.optionalDependencies) return []
  return Object.entries(props.optionalDependencies).sort(([a], [b]) => a.localeCompare(b))
})
</script>

<template>
  <div v-if="sortedOptionalDependencies.length > 0">
    <ul
      class="space-y-1 list-none m-0 p-0"
      :aria-label="$t('package.optional_dependencies.list_label')"
    >
      <li
        v-for="[dep, version] in sortedOptionalDependencies.slice(
          0,
          optionalDepsExpanded ? undefined : 10,
        )"
        :key="dep"
        class="flex items-center justify-between py-1 text-sm gap-2"
      >
        <NuxtLink
          :to="{ name: 'package', params: { package: dep.split('/') } }"
          class="font-mono text-fg-muted hover:text-fg transition-colors duration-200 truncate min-w-0"
        >
          {{ dep }}
        </NuxtLink>
        <NuxtLink
          :to="{ name: 'package', params: { package: [...dep.split('/'), 'v', version] } }"
          class="font-mono text-xs text-fg-subtle max-w-[50%] text-right truncate"
          :title="version"
        >
          {{ version }}
        </NuxtLink>
      </li>
    </ul>
    <button
      v-if="sortedOptionalDependencies.length > 10 && !optionalDepsExpanded"
      type="button"
      class="mt-2 font-mono text-xs text-fg-muted hover:text-fg transition-colors duration-200 rounded focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-fg/50"
      @click="optionalDepsExpanded = true"
    >
      {{
        $t('package.optional_dependencies.show_all', { count: sortedOptionalDependencies.length })
      }}
    </button>
  </div>
</template>
