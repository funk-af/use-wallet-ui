<script setup lang="ts">
import { h } from 'vue'
import { usePlugins } from './pluginContext'
import type { MenuRenderContext, MenuSlot } from './types'

const props = defineProps<{
  slot: MenuSlot
  ctx: MenuRenderContext
}>()

const { menuItemsBySlot } = usePlugins()
</script>

<template>
  <template v-for="item in menuItemsBySlot[props.slot]" :key="item.key">
    <component
      v-if="!item.enabled || item.enabled(props.ctx)"
      :is="() => h('div', {}, [item.render(props.ctx)])"
    />
  </template>
</template>
