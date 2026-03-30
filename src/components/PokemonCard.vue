<template>
  <div
    class="pokemon-card"
    :class="[
      `pokemon-card--${size}`,
      {
        'pokemon-card--selected': selected,
        'pokemon-card--disabled': disabled,
      },
    ]"
    @click="emit('click')"
  >
    <img :src="card.imgUrl" :alt="card.name" class="pokemon-card__img" />
    <div class="pokemon-card__info">
      <span class="pokemon-card__dex"
        >#{{ String(card.pokedexNumber).padStart(3, '0') }}</span
      >
      <span class="pokemon-card__name">{{ card.name }}</span>
      <span
        class="pokemon-card__type"
        :style="{ backgroundColor: getTypeColor(card.type) }"
      >
        {{ card.type }}
      </span>
      <div class="pokemon-card__stats">
        <span>❤️ {{ card.hp }}</span>
        <span>⚔️ {{ card.attack }}</span>
      </div>
      <NProgress
        v-if="currentHp !== undefined"
        type="line"
        :percentage="Math.round((currentHp / card.hp) * 100)"
        :color="hpColor(Math.round((currentHp / card.hp) * 100))"
        :show-indicator="false"
        style="margin-top: 4px"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { NProgress } from 'naive-ui'

import { useColors } from '@/composables/useColors'
import type { Card } from '@/types'

withDefaults(
  defineProps<{
    card: Card
    size?: 'sm' | 'md'
    selected?: boolean
    disabled?: boolean
    currentHp?: number
  }>(),
  {
    size: 'md',
    selected: false,
    disabled: false,
  },
)

const emit = defineEmits<{ click: [] }>()

const { getTypeColor, hpColor } = useColors()
</script>

<style scoped>
.pokemon-card {
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  overflow: hidden;
  cursor: pointer;
  transition:
    border-color 0.15s,
    box-shadow 0.15s,
    opacity 0.15s;
  background: white;
  user-select: none;
}

.pokemon-card--sm {
  width: 120px;
}

.pokemon-card--md {
  width: 180px;
}

.pokemon-card--selected {
  border-color: #18a058;
  box-shadow: 0 0 0 2px rgba(24, 160, 88, 0.35);
}

.pokemon-card--disabled {
  opacity: 0.4;
  cursor: not-allowed;
  pointer-events: none;
}

.pokemon-card__img {
  width: 100%;
  aspect-ratio: 1;
  object-fit: contain;
  background: #f5f5f5;
}

.pokemon-card__info {
  padding: 6px;
  display: flex;
  flex-direction: column;
  gap: 3px;
}

.pokemon-card--sm .pokemon-card__info {
  padding: 4px;
  font-size: 11px;
}

.pokemon-card__dex {
  color: #999;
  font-size: 0.8em;
}

.pokemon-card__name {
  font-weight: 600;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.pokemon-card__type {
  display: inline-block;
  padding: 1px 6px;
  border-radius: 10px;
  font-size: 0.75em;
  color: white;
  align-self: flex-start;
}

.pokemon-card__stats {
  display: flex;
  justify-content: space-between;
  font-size: 0.8em;
  color: #666;
}
</style>
