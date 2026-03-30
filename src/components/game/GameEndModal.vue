<template>
  <NModal :show="!!gameStore.gameResult" :mask-closable="false">
    <NCard style="width: 360px; text-align: center" :bordered="false">
      <NSpace vertical :size="24" align="center">
        <NText style="font-size: 48px">{{ isWinner ? '🏆' : '💀' }}</NText>
        <NText strong style="font-size: 24px">
          {{ isWinner ? 'Vous avez gagné !' : 'Vous avez perdu...' }}
        </NText>
        <NButton type="primary" block @click="handleBack"
          >Retour au lobby</NButton
        >
      </NSpace>
    </NCard>
  </NModal>
</template>

<script setup lang="ts">
import { NButton, NCard, NModal, NSpace, NText } from 'naive-ui'
import { computed } from 'vue'
import { useRouter } from 'vue-router'

import { ROUTES } from '@/router'
import { useGameStore } from '@/store/game'

const gameStore = useGameStore()
const router = useRouter()

const isWinner = computed(() => {
  if (!gameStore.gameResult) return false

  const winner = gameStore.gameResult.winner

  // Si le backend revoie directement le Socket ID du joueur gagnant
  if (winner === gameStore.socketId) return true

  // Si le backend renvoie 'host' ou 'guest'
  if (gameStore.gameState) {
    const youAreHost = gameStore.gameState.host.socketId === gameStore.socketId
    if (youAreHost && winner === 'host') return true
    if (!youAreHost && winner === 'guest') return true
  }

  return false
})

const handleBack = () => {
  gameStore.resetGame()
  router.push(ROUTES.HOME)
}
</script>
