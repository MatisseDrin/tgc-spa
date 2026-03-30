<template>
  <NGrid responsive="screen" cols="2 s:3 m:4 l:5 xl:6" :x-gap="12" :y-gap="12">
    <NGridItem v-for="card in cards" :key="card.id">
      <Card
        :card="card"
        size="sm"
        :selected="modelValue.includes(card.id)"
        :disabled="
          !modelValue.includes(card.id) && modelValue.length >= maxSelectable
        "
        @click="toggleCard(card.id)"
      />
    </NGridItem>
  </NGrid>
</template>

<script setup lang="ts">
import { NGrid, NGridItem } from 'naive-ui'

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
