<template>
  <div class="player-hand">
    <NSpace align="center" :size="8" style="margin-bottom: 8px">
      <NText depth="3" style="font-size: 12px"
        >Main ({{ board.hand.length }}/5)</NText
      >
      <NTag size="small">{{ board.deck.length }} cartes dans le deck</NTag>
    </NSpace>
    <div class="hand-cards">
      <PokemonCard
        v-for="(card, index) in board.hand"
        :key="card?.id || index"
        :card="card"
        size="sm"
        :disabled="!canPlay"
        @click="handlePlay(index)"
      />
      <NText v-if="board.hand.length === 0" depth="3" style="font-size: 12px">
        Aucune carte en main
      </NText>
    </div>
  </div>
</template>

<script setup lang="ts">
import { NSpace, NTag, NText } from 'naive-ui'
import { computed } from 'vue'

import PokemonCard from '@/components/PokemonCard.vue'
import { useGameStore } from '@/store/game'
import type { PlayerBoard } from '@/types'

const props = defineProps<{ board: PlayerBoard }>()

const gameStore = useGameStore()

const canPlay = computed(() => gameStore.isMyTurn && !props.board.activeCard)

const handlePlay = (cardIndex: number) => {
  if (!canPlay.value) return
  gameStore.playCard(cardIndex)
}
</script>

<style scoped>
.player-hand {
  padding-top: 12px;
  border-top: 1px solid #e0e0e0;
  margin-top: 12px;
}

.hand-cards {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}
</style>
