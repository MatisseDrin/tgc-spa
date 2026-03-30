<template>
  <NCard title="Jouer">
    <NGrid responsive="screen" cols="1 m:2" :x-gap="24" :y-gap="24">
      <!-- Créer une partie -->
      <NGridItem>
        <NCard title="Créer une partie" size="small">
          <NSpace vertical :size="12">
            <NSelect
              v-model:value="selectedDeckId"
              :options="deckOptions"
              placeholder="Choisir un deck..."
            />
            <NButton
              type="primary"
              block
              :disabled="!selectedDeckId"
              @click="handleCreate"
            >
              Créer la partie
            </NButton>
          </NSpace>
        </NCard>
      </NGridItem>

      <!-- Rejoindre une partie -->
      <NGridItem>
        <NCard title="Parties disponibles" size="small">
          <NEmpty
            v-if="gameStore.rooms.length === 0"
            description="Aucune partie disponible"
            style="padding: 16px 0"
          />
          <NSpace v-else vertical :size="8">
            <NCard
              v-for="room in gameStore.rooms"
              :key="room.id"
              size="small"
              embedded
            >
              <NSpace justify="space-between" align="center">
                <NText>Room #{{ room.id }}</NText>
                <NButton
                  size="small"
                  type="primary"
                  :disabled="
                    !selectedDeckId || room.hostSocketId === gameStore.socketId
                  "
                  @click="handleJoin(room.id)"
                >
                  Rejoindre
                </NButton>
              </NSpace>
            </NCard>
          </NSpace>
        </NCard>
      </NGridItem>
    </NGrid>
  </NCard>
</template>

<script setup lang="ts">
import {
  NButton,
  NCard,
  NEmpty,
  NGrid,
  NGridItem,
  NSelect,
  NSpace,
  NText,
  useMessage,
} from 'naive-ui'
import { computed, onMounted, ref, watch } from 'vue'

import { useApi } from '@/composables/useApi'
import { useGameStore } from '@/store/game'
import type { Deck } from '@/types'

const api = useApi()
const message = useMessage()
const gameStore = useGameStore()

const decks = ref<Deck[]>([])
const selectedDeckId = ref<number | null>(null)

const deckOptions = computed(() =>
  decks.value.map((d) => ({ label: d.name, value: d.id })),
)

onMounted(async () => {
  gameStore.connect()
  try {
    decks.value = await api.getMyDecks()
  } catch (error) {
    if (error instanceof Error) message.error(error.message)
  }
})

watch(
  () => gameStore.errorMessage,
  (err) => {
    if (err) {
      message.error(err)
      gameStore.errorMessage = null
    }
  },
)

const handleCreate = () => {
  if (!selectedDeckId.value) return
  gameStore.createRoom(selectedDeckId.value)
}

const handleJoin = (roomId: number) => {
  if (!selectedDeckId.value) return
  gameStore.joinRoom(roomId, selectedDeckId.value)
}
</script>
