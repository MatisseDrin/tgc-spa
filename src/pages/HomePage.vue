<template>
  <div class="page-container">
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

<style scoped>
.page-container {
  padding: 24px;
  max-width: 1200px;
  margin: 0 auto;
}

@media (max-width: 600px) {
  .page-container {
    padding: 12px;
  }
}
</style>
