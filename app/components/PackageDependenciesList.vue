<script setup lang="ts">
const props = defineProps<{
  dependencies: Record<string, string>
}>()

// Fetch outdated info for dependencies
const outdatedDeps = useOutdatedDependencies(() => props.dependencies)

// Expanded state
const depsExpanded = shallowRef(false)

// Sort dependencies alphabetically
const sortedDependencies = computed(() => {
  if (!props.dependencies) return []
  return Object.entries(props.dependencies).sort(([a], [b]) => a.localeCompare(b))
})
</script>

<template>
  <div v-if="sortedDependencies.length > 0">
    <ul class="space-y-1 list-none m-0 p-0" :aria-label="$t('package.dependencies.list_label')">
      <li
        v-for="[dep, version] in sortedDependencies.slice(0, depsExpanded ? undefined : 10)"
        :key="dep"
        class="flex items-center justify-between py-1 text-sm gap-2"
      >
        <NuxtLink
          :to="{ name: 'package', params: { package: dep.split('/') } }"
          class="font-mono text-fg-muted hover:text-fg transition-colors duration-200 truncate min-w-0"
        >
          {{ dep }}
        </NuxtLink>
        <span class="flex items-center gap-1">
          <span
            v-if="outdatedDeps[dep]"
            class="shrink-0"
            :class="getVersionClass(outdatedDeps[dep])"
            :title="getOutdatedTooltip(outdatedDeps[dep])"
            aria-hidden="true"
          >
            <span class="i-carbon-warning-alt w-3 h-3 block" />
          </span>
          <NuxtLink
            :to="{ name: 'package', params: { package: [...dep.split('/'), 'v', version] } }"
            class="font-mono text-xs text-right truncate"
            :class="getVersionClass(outdatedDeps[dep])"
            :title="outdatedDeps[dep] ? getOutdatedTooltip(outdatedDeps[dep]) : version"
          >
            {{ version }}
          </NuxtLink>
          <span v-if="outdatedDeps[dep]" class="sr-only">
            ({{ getOutdatedTooltip(outdatedDeps[dep]) }})
          </span>
        </span>
      </li>
    </ul>
    <button
      v-if="sortedDependencies.length > 10 && !depsExpanded"
      type="button"
      class="mt-2 font-mono text-xs text-fg-muted hover:text-fg transition-colors duration-200 rounded focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-fg/50"
      @click="depsExpanded = true"
    >
      {{ $t('package.dependencies.show_all', { count: sortedDependencies.length }) }}
    </button>
  </div>
</template>
