<script setup lang="ts">
import { computed } from 'vue'
import { useWallet } from '@txnlab/use-wallet-vue'
import { useAccountInfo, useNfd, NfdAvatar } from '@txnlab/use-wallet-ui-vue'
import { formatNumber, formatShortAddress } from '@txnlab/utils-ts'

const { activeAddress } = useWallet()
const nfdQuery = useNfd()
const accountQuery = useAccountInfo()

const nfd = computed(() => nfdQuery.data?.value ?? null)
const accountInfo = computed(() => accountQuery.data?.value)
const algoBalance = computed(() =>
  accountInfo.value ? Number(accountInfo.value.amount) / 1_000_000 : 0,
)
</script>

<template>
  <div
    v-if="!activeAddress"
    class="text-center p-8 bg-gray-50 dark:bg-slate-800/50 rounded-xl border border-gray-100 dark:border-slate-700"
  >
    <h3 class="text-lg font-medium text-gray-900 dark:text-white mb-2">
      Connect Your Wallet
    </h3>
    <p class="text-gray-600 dark:text-slate-300">
      Connect your Algorand wallet to view your NFD profile and balance
    </p>
  </div>

  <div
    v-else-if="nfdQuery.isLoading.value || accountQuery.isLoading.value"
    class="text-center p-8 bg-gray-50 dark:bg-slate-800/50 rounded-xl border border-gray-100 dark:border-slate-700"
  >
    <p class="text-gray-600 dark:text-slate-300">Loading wallet data...</p>
  </div>

  <div
    v-else
    class="p-8 bg-white dark:bg-slate-800/50 rounded-xl border border-gray-200 dark:border-slate-700 shadow-sm"
  >
    <div
      class="flex flex-col md:flex-row items-start md:items-center justify-between gap-6"
    >
      <div class="flex items-center gap-4">
        <NfdAvatar :nfd="nfd" :size="64" class="rounded-xl" />
        <div>
          <h2 class="text-xl font-bold text-gray-900 dark:text-white">
            {{ nfd?.name || formatShortAddress(activeAddress) }}
          </h2>
          <p
            v-if="nfd?.name"
            class="text-sm text-gray-500 dark:text-slate-400 font-mono"
          >
            {{ formatShortAddress(activeAddress) }}
          </p>
        </div>
      </div>

      <div class="px-4 py-3 bg-gray-50 dark:bg-slate-800 rounded-lg">
        <p class="text-sm text-gray-500 dark:text-slate-400">Balance</p>
        <p class="text-xl font-bold text-gray-900 dark:text-white">
          {{ formatNumber(algoBalance, { fractionDigits: 4 }) }} ALGO
        </p>
      </div>
    </div>

    <div
      v-if="
        nfd?.properties?.userDefined &&
        Object.keys(nfd.properties.userDefined).length > 0
      "
      class="mt-6 pt-6 border-t border-gray-100 dark:border-slate-700"
    >
      <h3 class="text-sm font-medium text-gray-700 dark:text-slate-300 mb-4">
        NFD Properties
      </h3>
      <div class="grid grid-cols-2 md:grid-cols-3 gap-4">
        <div
          v-for="[key, value] in Object.entries(nfd.properties.userDefined)"
          :key="key"
          class="bg-gray-50 dark:bg-slate-800 p-3 rounded-lg"
        >
          <p class="text-sm text-gray-500 dark:text-slate-400 mb-1">
            {{ key }}
          </p>
          <p class="text-sm font-medium text-gray-900 dark:text-white">
            {{ value }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
