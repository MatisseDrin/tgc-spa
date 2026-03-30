<template>
  <div class="action-bar">
    <NSpace align="center" justify="space-between">
      <NSpace align="center" :size="12">
        <NTag :type="gameStore.isMyTurn ? 'success' : 'warning'" size="medium">
          {{ gameStore.isMyTurn ? 'Votre tour' : 'Tour adversaire' }}
        </NTag>
        <NText v-if="gameStore.message" depth="2" style="font-size: 13px">
          {{ gameStore.message }}
        </NText>
      </NSpace>

      <NSpace :size="8">
        <NButton
          size="small"
          :disabled="!canDraw"
          @click="gameStore.drawCards()"
        >
          Piocher
        </NButton>
        <NButton
          size="small"
          type="warning"
          :disabled="!canAttack"
          @click="gameStore.attack()"
        >
          Attaquer
        </NButton>
        <NButton
          size="small"
          type="primary"
          :disabled="!gameStore.isMyTurn"
          @click="gameStore.endTurn()"
        >
          Fin de tour
        </NButton>
      </NSpace>
    </NSpace>
  </div>
</template>

<script setup lang="ts">
import { NButton, NSpace, NTag, NText } from 'naive-ui'
import { computed } from 'vue'

import { useGameStore } from '@/store/game'

const gameStore = useGameStore()

const canDraw = computed(
  () =>
    gameStore.isMyTurn &&
    (gameStore.myBoard?.hand.length ?? 0) < 5 &&
    (gameStore.myBoard?.deck.length ?? 0) > 0,
)

const canAttack = computed(
  () =>
    gameStore.isMyTurn &&
    !!gameStore.myBoard?.activeCard &&
    !!gameStore.opponentBoard?.activeCard,
)
</script>

<style scoped>
.action-bar {
  padding: 12px 24px;
  background: white;
  border-top: 1px solid #e0e0e0;
  border-bottom: 1px solid #e0e0e0;
}
</style>
