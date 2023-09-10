<script setup lang="ts">
import GameHeader from './components/GameHeader.vue'
import GameFigure from './components/GameFigure.vue'
import GameWrongLetters from './components/GameWrongLetters.vue'
import GameWord from './components/GameWord.vue'
import GamePopup from './components/GamePopup.vue'
import GameNotification from './components/GameNotification.vue'
import { computed, ref } from 'vue'
import { watch } from 'vue'

const word = ref('василий')
const letters = ref<string[]>([])
const correctLetters = computed(() => letters.value.filter(letter => word.value.includes(letter)))
const wrongLetters = computed(() => letters.value.filter(letter => !word.value.includes(letter)))
const notification = ref<InstanceType<typeof GameNotification> | null>(null)
const popup = ref<InstanceType<typeof GamePopup> | null>(null)

watch(wrongLetters, () => {
    if (wrongLetters.value.length === 6) {
        popup.value?.open('lose')
    }
})

watch(correctLetters, () => {
    if ([...word.value].every(x => correctLetters.value.includes(x))) {
        popup.value?.open('win')
    }
})

window.addEventListener('keydown', ({ key }) => {
    if (letters.value.includes(key)) {
        notification.value?.open()
        setTimeout(() => notification.value?.close(), 2000);
        return
    }

    if (/[а-яА-ЯёЁ]/.test(key)) {
        letters.value.push(key.toLowerCase())
    }
})

const restart = () => {
    letters.value = []
    popup.value?.close()

}
</script>

<template>
    <GameHeader />

    <div class="game-container">
        <GameFigure :wrong-letters-count="wrongLetters.length" />
        <GameWrongLetters :wrong-letters="wrongLetters" />
        <GameWord :word="word" :correct-letters="correctLetters" />
    </div>

    <GamePopup ref="popup" :word="word" @restart="restart"/>
    <GameNotification ref="notification"  />
</template>