<script setup lang="ts">
import { defineProps } from 'vue'
const props = defineProps({
  class: {
    type: String,
  },
})
</script>

<template>
  <div class="slidev-layout three-columns w-full h-full grid grid-cols-3">
    <div :class="props.class">
      <slot name="first" />
    </div>
    <div :class="props.class">
      <slot name="second" />
    </div>
    <div :class="props.class">
      <slot name="third" />
    </div>
  </div>
</template>
