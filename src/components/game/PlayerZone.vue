<template>
  <div class="player-zone" :class="{ 'player-zone--opponent': isOpponent }">
    <NSpace align="center" :size="8" style="margin-bottom: 12px">
      <NTag type="error">{{ board?.score ?? 0 }}/3 KO</NTag>
      <NText depth="3" style="font-size: 12px">
        {{ isOpponent ? 'Adversaire' : 'Vous' }}
      </NText>
    </NSpace>

    <div class="active-card-area">
      <PokemonCard
        v-if="board?.activeCard"
        :card="board.activeCard"
        :current-hp="board.activeCard.currentHp"
        size="md"
      />
      <div v-else class="placeholder">
        <NText depth="3">Aucune carte active</NText>
      </div>
    </div>

    <PlayerHand v-if="!isOpponent && board" :board="board" />
  </div>
</template>

<script setup lang="ts">
import { NSpace, NTag, NText } from 'naive-ui'

import PokemonCard from '@/components/PokemonCard.vue'
import type { PlayerBoard } from '@/types'

import PlayerHand from './PlayerHand.vue'

defineProps<{
  board: PlayerBoard | null
  isOpponent: boolean
}>()
</script>

<style scoped>
.player-zone {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 16px 24px;
  border-radius: 8px;
  background: rgba(0, 0, 0, 0.02);
}

.player-zone--opponent {
  background: rgba(208, 48, 80, 0.04);
}

.active-card-area {
  display: flex;
  justify-content: center;
  align-items: center;
  flex: 1;
}

.placeholder {
  width: 180px;
  height: 260px;
  border: 2px dashed #e0e0e0;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
