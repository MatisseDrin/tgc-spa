<template>
  <div style="padding: 24px; max-width: 1200px; margin: 0 auto">
    <NSpace vertical :size="24">
      <NSpace align="center">
        <NButton text @click="router.push(ROUTES.HOME)">← Retour</NButton>
        <NText strong style="font-size: 20px">Créer un deck</NText>
      </NSpace>

      <NCard>
        <NForm>
          <NFormItem label="Nom du deck">
            <NInput v-model:value="name" placeholder="Mon deck..." />
          </NFormItem>
          <NFormItem label="Rechercher une carte">
            <NInput
              v-model:value="searchTerm"
              placeholder="Nom de la carte..."
            />
          </NFormItem>
          <NFormItem
            :label="`Cartes sélectionnées (${selectedCards.length}/10)`"
          >
            <NSpin :show="loadingCards">
              <CardGrid
                v-model="selectedCards"
                :cards="filteredCards"
                :max-selectable="10"
              />
            </NSpin>
          </NFormItem>
        </NForm>
        <NSpace justify="end" style="margin-top: 16px">
          <NButton @click="router.push(ROUTES.HOME)">Annuler</NButton>
          <NButton
            type="primary"
            :loading="submitting"
            :disabled="!name.trim() || selectedCards.length !== 10"
            @click="handleSubmit"
          >
            Créer le deck
          </NButton>
        </NSpace>
      </NCard>
    </NSpace>
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
import { useRouter } from 'vue-router'

import CardGrid from '@/components/CardGrid.vue'
import { useApi } from '@/composables/useApi'
import { ROUTES } from '@/router'
import type { Card } from '@/types'

const router = useRouter()
const api = useApi()
const message = useMessage()

const name = ref('')
const searchTerm = ref('')
const selectedCards = ref<number[]>([])
const cards = ref<Card[]>([])
const loadingCards = ref(false)
const submitting = ref(false)

const filteredCards = computed(() =>
  cards.value.filter(
    (c) =>
      selectedCards.value.includes(c.id) ||
      c.name.toLowerCase().includes(searchTerm.value.toLowerCase()),
  ),
)

onMounted(async () => {
  loadingCards.value = true
  try {
    cards.value = await api.getCards()
  } catch (error) {
    if (error instanceof Error) message.error(error.message)
  } finally {
    loadingCards.value = false
  }
})

const handleSubmit = async () => {
  if (!name.value.trim() || selectedCards.value.length !== 10) return
  submitting.value = true
  try {
    await api.createDeck({
      name: name.value.trim(),
      cards: selectedCards.value,
    })
    message.success('Deck créé !')
    router.push(ROUTES.HOME)
  } catch (error) {
    if (error instanceof Error) message.error(error.message)
  } finally {
    submitting.value = false
  }
}
</script>
