<script setup lang="ts">
import { Icon } from '@iconify/vue'
import { useIntervalFn } from '@vueuse/core'
import { computed, ref, watch } from 'vue'
import attendeeList from './attendee.json'
import RandomLetter from './RandomLetter.vue'

const codeLength = 8

const winnerAttendee = ref<{ code: string, name: string } | null>(null)

const previewWinnerName = ref<string | null>(null)

const initialStopLetters = ['G', 'O', 'O', 'D', 'L', 'U', 'C', 'K']

const stopLetters = ref<(string | null)[]>(initialStopLetters)

const countdownIndex = ref(0)

const randomActive = ref(false)

const winnerCode = computed(() => winnerAttendee.value?.code || '')

const countdownInterval = useIntervalFn(() => {
  stopLetters.value[countdownIndex.value] = winnerCode.value[countdownIndex.value]
  countdownIndex.value++
}, 500, { immediate: false, immediateCallback: true })

watch(countdownIndex, (value) => {
  if (value === codeLength) {
    randomActive.value = false
    countdownIndex.value = 0
    countdownInterval.pause()
    previewWinnerName.value = winnerAttendee.value?.name || ''
  }
})

function clickStart() {
  randomActive.value = true
  previewWinnerName.value = null
  winnerAttendee.value = null
  stopLetters.value = Array.from({ length: codeLength }, () => null)
}

function clickStop() {
  if (!countdownInterval.isActive.value) {
    const randomAttendee = attendeeList[Math.floor(Math.random() * attendeeList.length)]

    winnerAttendee.value = {
      code: randomAttendee.code,
      name: randomAttendee.name,
    }

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
  <div class="container mx-auto flex h-screen flex-col items-center justify-center gap-24 p-4">
    <div class="relative">
      <div v-if="previewWinnerName" class="animate__animated animate__fadeInDown absolute bottom-48 w-full text-center text-4xl lg:bottom-24">
        {{ previewWinnerName }}
      </div>
      <div class="flex max-w-md flex-wrap items-center justify-center gap-16 lg:max-w-full">
        <RandomLetter v-for="(letter, index) in stopLetters" :key="index" :stop-letter="letter" />
      </div>
    </div>

    <div class="flex gap-16">
      <button
        v-if="randomActive"
        class="rounded-lg border-2 border-primary p-1.5 transition-colors ease-in-out hover:bg-primary [&_svg]:hover:!text-white"
        @click="clickStop"
      >
        <Icon class="text-2xl transition-colors ease-in-out" icon="solar:pause-bold" color="#44bd87" />
      </button>
      <button
        v-else
        class="rounded-lg border-2 border-primary p-1.5 transition-colors ease-in-out hover:bg-primary [&_svg]:hover:!text-white"
        @click="clickStart"
      >
        <Icon class="text-2xl transition-colors ease-in-out" icon="solar:play-bold" color="#44bd87" />
      </button>

      <button
        class="rounded-lg border-2 border-white p-1.5 transition-colors ease-in-out hover:bg-white [&_svg]:hover:!text-black"
        @click="clickReset"
      >
        <Icon class="text-2xl transition-colors ease-in-out" icon="solar:restart-bold" color="white" />
      </button>
    </div>
  </div>
</template>
