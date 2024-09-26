<script setup lang="ts">
import { Icon } from '@iconify/vue'
import { useIntervalFn } from '@vueuse/core'
import { ref, watch } from 'vue'
import RandomLetter from './RandomLetter.vue'

const codeLength = 8

const winnerCode = 'M9CW2LEJ'

const initialStopLetters = ['G', 'O', 'O', 'D', 'L', 'U', 'C', 'K']

const stopLetters = ref<(string | null)[]>(initialStopLetters)

const previewWinnerName = ref<string | null>(null)

const countdownIndex = ref(0)

const randomActive = ref(false)

const countdownInterval = useIntervalFn(() => {
  stopLetters.value[countdownIndex.value] = winnerCode[countdownIndex.value]
  countdownIndex.value++
}, 500, { immediate: false, immediateCallback: true })

watch(countdownIndex, (value) => {
  if (value === codeLength) {
    randomActive.value = false
    countdownIndex.value = 0
    countdownInterval.pause()
    previewWinnerName.value = 'Runyasak Chaengnaimuang'
  }
})

function clickStart() {
  randomActive.value = true
  previewWinnerName.value = null
  stopLetters.value = Array.from({ length: initialStopLetters.length }, () => null)
}

function clickStop() {
  if (!countdownInterval.isActive.value) {
    countdownInterval.resume()
  }
}

function clickReset() {
  randomActive.value = false
  countdownIndex.value = 0
  stopLetters.value = initialStopLetters
  previewWinnerName.value = null
}
</script>

<template>
  <div class="container mx-auto flex h-screen flex-col items-center justify-center p-4">
    <div class="relative">
      <div v-if="previewWinnerName" class="animate__animated animate__fadeInDown absolute bottom-24 w-full text-center text-4xl">
        {{ previewWinnerName }}
      </div>
      <div class="flex gap-16">
        <RandomLetter v-for="(letter, index) in stopLetters" :key="index" :stop-letter="letter" />
      </div>
    </div>

    <div class="flex gap-8 pt-24">
      <button
        v-if="randomActive"
        class="rounded-lg border-2 border-primary p-2 transition-colors ease-in-out hover:bg-primary [&_svg]:hover:!text-white"
        @click="clickStop"
      >
        <Icon class="text-2xl transition-colors ease-in-out" icon="solar:pause-bold" color="#44bd87" />
      </button>
      <button
        v-else
        class="rounded-lg border-2 border-primary p-2 transition-colors ease-in-out hover:bg-primary [&_svg]:hover:!text-white"
        @click="clickStart"
      >
        <Icon class="text-2xl transition-colors ease-in-out" icon="solar:play-bold" color="#44bd87" />
      </button>

      <button
        class="rounded-lg border-2 border-white p-2 transition-colors ease-in-out hover:bg-white [&_svg]:hover:!text-black"
        @click="clickReset"
      >
        <Icon class="text-2xl transition-colors ease-in-out" icon="solar:restart-bold" color="white" />
      </button>
    </div>
  </div>
</template>
