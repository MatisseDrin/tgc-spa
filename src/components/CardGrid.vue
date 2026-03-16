<template>
  <div class="card-grid">
    <Card
      v-for="card in cards"
      :key="card.id"
      :card="card"
      size="sm"
      :selected="modelValue.includes(card.id)"
      :disabled="
        !modelValue.includes(card.id) && modelValue.length >= maxSelectable
      "
      @click="toggleCard(card.id)"
    />
  </div>
</template>

<script setup lang="ts">
import type { Card as CardType } from '@/types'

import Card from './PokemonCard.vue'

const props = withDefaults(
  defineProps<{
    cards: CardType[]
    modelValue: number[]
    maxSelectable?: number
  }>(),
  {
    maxSelectable: 10,
  },
)

const emit = defineEmits<{ 'update:modelValue': [value: number[]] }>()

const toggleCard = (id: number) => {
  const idx = props.modelValue.indexOf(id)
  if (idx !== -1) {
    emit(
      'update:modelValue',
      props.modelValue.filter((c) => c !== id),
    )
  } else if (props.modelValue.length < props.maxSelectable) {
    emit('update:modelValue', [...props.modelValue, id])
  }
}
</script>

<style scoped>
.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}
</style>
