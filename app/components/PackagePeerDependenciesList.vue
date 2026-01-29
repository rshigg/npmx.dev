<script setup lang="ts">
const props = defineProps<{
  peerDependencies: Record<string, string>
  peerDependenciesMeta?: Record<string, { optional?: boolean }>
}>()

// Expanded state
const peerDepsExpanded = shallowRef(false)

// Sort peer dependencies alphabetically, with required first then optional
const sortedPeerDependencies = computed(() => {
  if (!props.peerDependencies) return []

  return Object.entries(props.peerDependencies)
    .map(([name, version]) => ({
      name,
      version,
      optional: props.peerDependenciesMeta?.[name]?.optional ?? false,
    }))
    .sort((a, b) => {
      // Required first, then optional
      if (a.optional !== b.optional) return a.optional ? 1 : -1
      return a.name.localeCompare(b.name)
    })
})
</script>

<template>
  <div v-if="sortedPeerDependencies.length > 0">
    <ul
      class="space-y-1 list-none m-0 p-0"
      :aria-label="$t('package.peer_dependencies.list_label')"
    >
      <li
        v-for="peer in sortedPeerDependencies.slice(0, peerDepsExpanded ? undefined : 10)"
        :key="peer.name"
        class="flex items-center justify-between py-1 text-sm gap-2"
      >
        <div class="flex items-center gap-2 min-w-0">
          <NuxtLink
            :to="{ name: 'package', params: { package: peer.name.split('/') } }"
            class="font-mono text-fg-muted hover:text-fg transition-colors duration-200 truncate"
          >
            {{ peer.name }}
          </NuxtLink>
          <span
            v-if="peer.optional"
            class="px-1 py-0.5 font-mono text-[10px] text-fg-subtle bg-bg-muted border border-border rounded shrink-0"
            :title="$t('package.dependencies.optional')"
          >
            {{ $t('package.dependencies.optional') }}
          </span>
        </div>
        <NuxtLink
          :to="{
            name: 'package',
            params: { package: [...peer.name.split('/'), 'v', peer.version] },
          }"
          class="font-mono text-xs text-fg-subtle max-w-[40%] text-right truncate"
          :title="peer.version"
        >
          {{ peer.version }}
        </NuxtLink>
      </li>
    </ul>
    <button
      v-if="sortedPeerDependencies.length > 10 && !peerDepsExpanded"
      type="button"
      class="mt-2 font-mono text-xs text-fg-muted hover:text-fg transition-colors duration-200 rounded focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-fg/50"
      @click="peerDepsExpanded = true"
    >
      {{ $t('package.peer_dependencies.show_all', { count: sortedPeerDependencies.length }) }}
    </button>
  </div>
</template>
