<template>
  <div class="page-container">
    <NSpin :show="loading">
      <NSpace v-if="!loading" vertical :size="24">
        <NSpace align="center">
          <NButton text @click="router.push(`/decks/${deckId}`)"
            >← Retour</NButton
          >
          <NText strong style="font-size: 20px">Modifier le deck</NText>
        </NSpace>

        <NCard>
          <NForm>
            <NFormItem label="Nom du deck">
              <NInput v-model:value="name" placeholder="Mon deck..." />
            </NFormItem>
            <NFormItem label="Rechercher une carte">
              <NInput
                v-model:value="searchTerm"
                placeholder="Nom du Pokémon..."
              />
            </NFormItem>
            <NFormItem
              :label="`Cartes sélectionnées (${selectedCards.length}/10)`"
            >
              <CardGrid
                v-model="selectedCards"
                :cards="filteredCards"
                :max-selectable="10"
              />
            </NFormItem>
          </NForm>
          <NSpace justify="end" style="margin-top: 16px">
            <NButton @click="router.push(`/decks/${deckId}`)">Annuler</NButton>
            <NButton
              type="primary"
              :loading="submitting"
              :disabled="!name.trim() || selectedCards.length !== 10"
              @click="handleSubmit"
            >
              Enregistrer
            </NButton>
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
  NForm,
  NFormItem,
  NInput,
  NSpace,
  NSpin,
  NText,
  useMessage,
} from 'naive-ui'
import { computed, onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

import CardGrid from '@/components/CardGrid.vue'
import { useApi } from '@/composables/useApi'
import type { Card } from '@/types'

const route = useRoute()
const router = useRouter()
const api = useApi()
const message = useMessage()

const deckId = route.params.id as string

const name = ref('')
const searchTerm = ref('')
const selectedCards = ref<number[]>([])
const cards = ref<Card[]>([])
const loading = ref(false)
const submitting = ref(false)

const filteredCards = computed(() =>
  cards.value.filter(
    (c) =>
      selectedCards.value.includes(c.id) ||
      c.name.toLowerCase().includes(searchTerm.value.toLowerCase()),
  ),
)

onMounted(async () => {
  loading.value = true
  try {
    const [deck, allCards] = await Promise.all([
      api.getDeck(deckId),
      api.getCards(),
    ])
    name.value = deck.name
    selectedCards.value = deck.cards.map((dc) => dc.cardId)
    cards.value = allCards
  } catch (error) {
    if (error instanceof Error) message.error(error.message)
  } finally {
    loading.value = false
  }
})

const handleSubmit = async () => {
  if (!name.value.trim() || selectedCards.value.length !== 10) return
  submitting.value = true
  try {
    await api.updateDeck(deckId, {
      name: name.value.trim(),
      cards: selectedCards.value,
    })
    message.success('Deck mis à jour !')
    router.push(`/decks/${deckId}`)
  } catch (error) {
    if (error instanceof Error) message.error(error.message)
  } finally {
    submitting.value = false
  }
}
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
