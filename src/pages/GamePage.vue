<template>
  <div class="game-page">
    <PlayerZone :board="gameStore.opponentBoard" :is-opponent="true" />
    <ActionBar />
    <PlayerZone :board="gameStore.myBoard" :is-opponent="false" />
    <GameEndModal />
  </div>
</template>

<script setup lang="ts">
import ActionBar from '@/components/game/ActionBar.vue'
import GameEndModal from '@/components/game/GameEndModal.vue'
import PlayerZone from '@/components/game/PlayerZone.vue'
import { useGameStore } from '@/store/game'

const gameStore = useGameStore()
</script>

<style scoped>
.game-page {
  display: flex;
  flex-direction: column;
  height: 100vh;
  overflow: auto;
}
</style>
