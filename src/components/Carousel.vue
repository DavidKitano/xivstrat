<script setup lang="ts">
import type { HTMLAttributes } from 'vue'

import { watchOnce } from '@vueuse/core'
import { ref } from 'vue'

import type { CarouselApi } from '@/components/shadcn-vue/carousel'

import {
  Carousel,
  CarouselContent,
  CarouselItem,
  CarouselNext,
  CarouselPrevious,
} from '@/components/shadcn-vue/carousel'

const props = defineProps<{
  slots: Slot[]
  loop?: boolean
  class?: HTMLAttributes['class']
}>()
const api = ref<CarouselApi>()
const totalCount = ref(0)
const current = ref(0)

function setApi(val: CarouselApi) {
  api.value = val
}

watchOnce(api, (api) => {
  if (!api) {
    return
  }

  totalCount.value = api.scrollSnapList().length
  current.value = api.selectedScrollSnap() + 1

  api.on('select', () => {
    current.value = api.selectedScrollSnap() + 1
  })
})
type Slot = string | number
</script>

<template>
  <Carousel
    :opts="{
      loop: props.loop,
    }"
    :class="props.class"
    @init-api="setApi"
  >
    <CarouselContent>
      <CarouselItem v-for="slot in props.slots" :key="slot">
        <slot :name="slot" />
      </CarouselItem>
    </CarouselContent>
    <CarouselPrevious />
    <CarouselNext />
  </Carousel>
</template>
