<script setup lang="ts">
import { useIntervalFn } from '@vueuse/core'
import { ref, watchEffect } from 'vue'

const props = withDefaults(defineProps<{ stopLetter: string | null }>(), { stopLetter: '' })

const allAlphabets = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'

const previewCharacter = ref('')

const { pause, resume } = useIntervalFn(() => {
  previewCharacter.value = allAlphabets[Math.ceil(Math.random() * allAlphabets.length - 1)]
}, 100, { immediateCallback: true })

watchEffect(() => {
  if (props.stopLetter) {
    pause()
  }
  else {
    resume()
  }
})
</script>

<template>
  <div class="w-10 text-center text-4xl font-bold transition-colors ease-in-out" :class="{ 'text-primary': !!stopLetter }">
    {{ stopLetter ? stopLetter : previewCharacter }}
  </div>
</template>
