<template>
  <div style="padding: 24px; max-width: 1200px; margin: 0 auto">
    <NSpace vertical :size="32">
      <LobbyPanel />
      <DeckList />
    </NSpace>
  </div>
</template>

<script setup lang="ts">
import DeckList from '@/components/DeckList.vue'
import LobbyPanel from '@/components/LobbyPanel.vue'
</script>
