<template>
  <div class="page-container">
    <NSpin :show="loading">
      <NSpace v-if="deck" vertical :size="24">
        <NSpace justify="space-between" align="center">
          <NSpace align="center">
            <NButton text @click="router.push(ROUTES.HOME)">← Retour</NButton>
            <NText strong style="font-size: 20px">{{ deck.name }}</NText>
          </NSpace>
          <NButton
            type="primary"
            @click="router.push(`/decks/${deck.id}/edit`)"
          >
            Modifier
          </NButton>
        </NSpace>

        <NCard>
          <NGrid
            responsive="screen"
            cols="2 s:3 m:4 l:5 xl:6"
            :x-gap="12"
            :y-gap="12"
          >
            <NGridItem v-for="card in deckCards" :key="card.id">
              <Card :card="card" size="sm" />
            </NGridItem>
          </NGrid>
        </NCard>
      </NSpace>
    </NSpin>
  </div>
</template>

<script setup lang="ts">
import {
  NButton,
  NCard,
  NGrid,
  NGridItem,
  NSpace,
  NSpin,
  NText,
  useMessage,
} from 'naive-ui'
import { computed, onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

import Card from '@/components/PokemonCard.vue'
import { useApi } from '@/composables/useApi'
import { ROUTES } from '@/router'
import type { Card as CardType, Deck } from '@/types'

const route = useRoute()
const router = useRouter()
const api = useApi()
const message = useMessage()

const loading = ref(false)
const deck = ref<Deck | null>(null)
const allCards = ref<CardType[]>([])

const deckCards = computed(() => {
  if (!deck.value) return []
  return deck.value.cards
    .map((dc) => allCards.value.find((c) => c.id === dc.cardId))
    .filter((c): c is CardType => c !== undefined)
})

onMounted(async () => {
  loading.value = true
  try {
    const id = route.params.id as string
    ;[deck.value, allCards.value] = await Promise.all([
      api.getDeck(id),
      api.getCards(),
    ])
  } catch (error) {
    if (error instanceof Error) message.error(error.message)
  } finally {
    loading.value = false
  }
})
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
