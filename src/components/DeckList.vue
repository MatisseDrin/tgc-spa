<template>
  <div>
    <NSpace justify="space-between" align="center" style="margin-bottom: 24px">
      <NText strong style="font-size: 18px">Mes decks</NText>
      <NButton type="primary" @click="router.push(ROUTES.CREATE_DECK)"
        >+ Nouveau deck</NButton
      >
    </NSpace>

    <NSpin :show="loading">
      <NEmpty
        v-if="!loading && decks.length === 0"
        description="Aucun deck pour l'instant"
      />
      <NSpace v-else vertical :size="24">
        <NCard v-for="deck in decks" :key="deck.id" :title="deck.name">
          <NGrid
            responsive="screen"
            cols="2 s:3 m:4 l:5 xl:6"
            :x-gap="12"
            :y-gap="12"
          >
            <NGridItem v-for="card in getDeckCards(deck)" :key="card.id">
              <Card :card="card" size="sm" />
            </NGridItem>
          </NGrid>
          <NSpace justify="end" style="margin-top: 16px">
            <NButton size="small" @click="router.push(`/decks/${deck.id}`)"
              >Détails</NButton
            >
            <NButton size="small" @click="router.push(`/decks/${deck.id}/edit`)"
              >Modifier</NButton
            >
            <NPopconfirm @positive-click="handleDelete(deck.id)">
              <template #trigger>
                <NButton size="small" type="error">Supprimer</NButton>
              </template>
              Supprimer ce deck ?
            </NPopconfirm>
          </NSpace>
        </NCard>
      </NSpace>
    </NSpin>
  </div>
</template>

<script setup lang="ts">
import {
  NButton,
  NCard,
  NEmpty,
  NGrid,
  NGridItem,
  NPopconfirm,
  NSpace,
  NSpin,
  NText,
  useMessage,
} from 'naive-ui'
import { onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'

import Card from '@/components/PokemonCard.vue'
import { useApi } from '@/composables/useApi'
import { ROUTES } from '@/router'
import type { Card as CardType, Deck } from '@/types'

const router = useRouter()
const api = useApi()
const message = useMessage()

const loading = ref(false)
const decks = ref<Deck[]>([])
const allCards = ref<CardType[]>([])

const getDeckCards = (deck: Deck) =>
  deck.cards
    .map((dc) => allCards.value.find((c) => c.id === dc.cardId))
    .filter((c): c is CardType => c !== undefined)

const fetchDecks = async () => {
  loading.value = true
  try {
    ;[decks.value, allCards.value] = await Promise.all([
      api.getMyDecks(),
      api.getCards(),
    ])
  } catch (error) {
    if (error instanceof Error) message.error(error.message)
  } finally {
    loading.value = false
  }
}

const handleDelete = async (id: number) => {
  try {
    await api.deleteDeck(id)
    message.success('Deck supprimé')
    await fetchDecks()
  } catch (error) {
    if (error instanceof Error) message.error(error.message)
  }
}

onMounted(fetchDecks)
</script>
