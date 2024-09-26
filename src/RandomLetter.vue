<script setup lang="ts">
import { useIntervalFn } from '@vueuse/core'
import { ref, watchEffect } from 'vue'

const props = withDefaults(defineProps<{ stopLetter: string | null }>(), { stopLetter: '' })

const allAlphabets = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'

const previewCharacter = ref('')

const { pause, resume } = useIntervalFn(() => {
  previewCharacter.value = allAlphabets[Math.ceil(Math.random() * allAlphabets.length - 1)]
}, 100)

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
  <div class="text-xl font-bold">
    {{ stopLetter ? stopLetter : previewCharacter }}
  </div>
</template>
