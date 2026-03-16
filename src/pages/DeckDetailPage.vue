<template>
  <div style="padding: 24px; max-width: 1200px; margin: 0 auto">
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
          <div class="card-grid">
            <Card
              v-for="card in deckCards"
              :key="card.id"
              :card="card"
              size="sm"
            />
          </div>
        </NCard>
      </NSpace>
    </NSpin>
  </div>
</template>

<script setup lang="ts">
import { NButton, NCard, NSpace, NSpin, NText, useMessage } from 'naive-ui'
import { computed, onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

import Card from '@/components/Card.vue'
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
.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}
</style>
