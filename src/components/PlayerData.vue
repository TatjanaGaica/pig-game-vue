<script setup>
import { ref, watch } from 'vue'
defineProps({
  isActive: Boolean,
  score: Number,
  wins: Number,
  currentScore: Number,
  playerHasWon: Boolean,
  activePlayer: Number,
  playerName: String
})
const emit = defineEmits(['nameButtonActiveChanged', 'playerNameChanged'])
const nameButtons0ElIsActive = ref(true)
watch(nameButtons0ElIsActive, (newValue) => {
  emit('nameButtonActiveChanged', newValue)
})
const editButton0ElOnClick = function () {
  nameButtons0ElIsActive.value = true
}
const input0ElTextContent = ref('')
const input0ElKeyUp = function (event) {
  if (event.code === 'Enter') {
    event.preventDefault()
    submitButton0ElOnClick()
  }
}
function submitButton0ElOnClick() {
  emit('playerNameChanged', input0ElTextContent.value)
  nameButtons0ElIsActive.value = false
}
</script>

<template>
  <section
    class="player player--0"
    :class="{
      'player--active': isActive,
      'player--winner': playerHasWon && isActive
    }"
  >
    <div class="player-name">
      <input
        maxlength="10"
        class="player-name-input"
        :class="{ hidden: !nameButtons0ElIsActive }"
        id="player-name-input-zero"
        v-model="input0ElTextContent"
        @keyup="input0ElKeyUp"
      />
      <input
        type="button"
        value="Submit"
        class="btn player-name-input-button"
        :class="{ hidden: !nameButtons0ElIsActive }"
        id="player-name-input-button-zero"
        @click="submitButton0ElOnClick"
      />
      <input
        type="button"
        value="Edit"
        class="btn player-name-input-button"
        :class="{ hidden: nameButtons0ElIsActive }"
        id="player-name-input-edit-button-zero"
        @click="editButton0ElOnClick"
      />
      <h2 class="name" :class="{ hidden: nameButtons0ElIsActive }" id="name--0">
        {{ playerName }}
      </h2>
    </div>
    <p class="score" id="score--0">{{ score }}</p>
    <div class="current">
      <div v-if="isActive">
        <p class="current-label">Current</p>
        <p class="current-score" id="current--0">{{ currentScore }}</p>
      </div>
      <div>
        <p class="current-label">Wins</p>
        <p class="current-score win--score" id="wins--0">{{ wins }}</p>
      </div>
    </div>
  </section>
</template>
