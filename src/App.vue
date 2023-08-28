<script setup>
import { computed, ref } from 'vue'
import PlayerData from './components/PlayerData.vue'

// Variables
const nameButtons0ElIsActive = ref(true)
const nameButtons1ElIsActive = ref(true)
const playerHasWon = ref(false)
const diceElIsActive = ref(false)
const diceElSource = ref('')
const firstPlayerName = ref('')
const secondPlayerName = ref('')
const currentScore = ref(0)
const activePlayer = ref(0)
const scores = ref([0, 0])
const wins = ref([0, 0])
const player0IsActive = computed(() => activePlayer.value === 0)
const player1IsActive = computed(() => !player0IsActive.value)
const gameButtonsIsActive = computed(
  () => !nameButtons0ElIsActive.value && !nameButtons1ElIsActive.value
)
let playing = true

// Methods
function nameActiveChanged(newName, playerNumber) {
  if (playerNumber === 0) {
    firstPlayerName.value = newName
  } else secondPlayerName.value = newName
}

function handleNameButtonActiveChanged(newValue, playerNumber) {
  if (playerNumber === 0) {
    nameButtons0ElIsActive.value = newValue
  } else nameButtons1ElIsActive.value = newValue
}

function btnRollOnClick() {
  if (!playing) return
  // 1. Generating a randon dice roll
  const dice = Math.trunc(Math.random() * 6) + 1

  // 2. Display dice
  diceElIsActive.value = true
  diceElSource.value = `dice-${dice}.png`

  // 3. Check for rolled 1: if true, switch to next player
  if (dice !== 1) {
    // Add dice to current score
    currentScore.value += dice
  } else {
    // Switch to next player
    switchPlayer()
  }
}

function switchPlayer() {
  currentScore.value = 0
  activePlayer.value = activePlayer.value === 0 ? 1 : 0
  player0IsActive.value = !player0IsActive.value
}

function btnHoldOnClick() {
  if (playing) {
    // 1. Add current score to active player's score
    scores.value[activePlayer.value] += currentScore.value
    // 2. Check if player's score is >= 10
    if (scores.value[activePlayer.value] >= 5) {
      // Finish the game
      playing = false
      diceElIsActive.value = false
      wins.value[activePlayer.value] += 1
      playerHasWon.value = true
      alert(`${activePlayer.value === 0 ? firstPlayerName.value : secondPlayerName.value} has WON!`)
    } else {
      // 3. Switch to next player
      switchPlayer()
    }
  }
}

function btnNewGameOnClick() {
  scores.value[0] = 0
  scores.value[1] = 0
  currentScore.value = 0
  player0IsActive.value = true
  diceElIsActive.value = false
  playing = true
  diceElIsActive.value = true
  playerHasWon.value = false
}
</script>

<template>
  <main>
    <PlayerData
      :isActive="player0IsActive"
      :score="scores[0]"
      :currentScore="currentScore"
      :wins="wins[0]"
      :activePlayer="activePlayer"
      :playerHasWon="playerHasWon"
      :playerName="firstPlayerName"
      @nameButtonActiveChanged="(newValue) => handleNameButtonActiveChanged(newValue, 0)"
      @playerNameChanged="(newName) => nameActiveChanged(newName, 0)"
    ></PlayerData>
    <PlayerData
      :isActive="player1IsActive"
      :score="scores[1]"
      :currentScore="currentScore"
      :wins="wins[1]"
      :activePlayer="activePlayer"
      :playerHasWon="playerHasWon"
      :playerName="secondPlayerName"
      @nameButtonActiveChanged="(newValue) => handleNameButtonActiveChanged(newValue, 1)"
      @playerNameChanged="(newName) => nameActiveChanged(newName, 1)"
    ></PlayerData>
    <img alt="Playing dice" class="dice" :class="{ hidden: !diceElIsActive }" :src="diceElSource" />
    <button
      class="btn btn--new"
      :class="{ hidden: !gameButtonsIsActive }"
      @click="btnNewGameOnClick"
    >
      🔄 New game
    </button>
    <button class="btn btn--roll" :class="{ hidden: !gameButtonsIsActive }" @click="btnRollOnClick">
      🎲 Roll dice
    </button>
    <button class="btn btn--hold" :class="{ hidden: !gameButtonsIsActive }" @click="btnHoldOnClick">
      📥 Hold
    </button>
  </main>
</template>

<style>
/* LAYOUT */
main {
  position: relative;
  width: 100rem;
  height: 60rem;
  background-color: rgba(255, 255, 255, 0.35);
  backdrop-filter: blur(200px);
  filter: blur();
  box-shadow: 0 3rem 5rem rgba(0, 0, 0, 0.25);
  border-radius: 9px;
  overflow: hidden;
  display: flex;
}

.player {
  flex: 50%;
  padding: 9rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: all 0.75s;
}

/* ELEMENTS */
.name {
  position: relative;
  font-size: 4rem;
  text-transform: uppercase;
  letter-spacing: 1px;
  word-spacing: 2px;
  font-weight: 300;
  margin-bottom: 1rem;
}

.score {
  font-size: 8rem;
  font-weight: 300;
  color: #c7365f;
  margin-bottom: auto;
}

.player--active {
  background-color: rgba(255, 255, 255, 0.4);
}
.player--active .name {
  font-weight: 700;
}
.player--active .score {
  font-weight: 400;
}

.player--active .current {
  opacity: 1;
}

.current {
  background-color: #c7365f;
  opacity: 0.8;
  border-radius: 9px;
  color: #fff;
  width: 65%;
  padding: 2rem;
  text-align: center;
  transition: all 0.75s;
}

.current-label {
  text-transform: uppercase;
  margin-bottom: 1rem;
  font-size: 1.7rem;
  color: #ddd;
}

.current-score {
  font-size: 3.5rem;
}

/* ABSOLUTE POSITIONED ELEMENTS */
.btn {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  color: #444;
  background: none;
  border: none;
  font-family: inherit;
  font-size: 1.8rem;
  text-transform: uppercase;
  cursor: pointer;
  font-weight: 400;
  transition: all 0.2s;

  background-color: white;
  background-color: rgba(255, 255, 255, 0.6);
  backdrop-filter: blur(10px);

  padding: 0.7rem 2.5rem;
  border-radius: 50rem;
  box-shadow: 0 1.75rem 3.5rem rgba(0, 0, 0, 0.1);
}

.btn::first-letter {
  font-size: 2.4rem;
  display: inline-block;
  margin-right: 0.7rem;
}

.btn--new {
  top: 4rem;
}
.btn--roll {
  top: 39.3rem;
}
.btn--hold {
  top: 46.1rem;
}

.btn:active {
  transform: translate(-50%, 3px);
  box-shadow: 0 1rem 2rem rgba(0, 0, 0, 0.15);
}

.btn:focus {
  outline: none;
}

.dice {
  position: absolute;
  left: 50%;
  top: 16.5rem;
  transform: translateX(-50%);
  height: 10rem;
  box-shadow: 0 2rem 5rem rgba(0, 0, 0, 0.2);
}

.player--winner {
  background-color: #2f2f2f;
}

.player--winner .name {
  font-weight: 700;
  color: #c7365f;
}

.hidden {
  display: none;
}

input {
  font-family: 'Nunito', sans-serif;
  font-weight: 400;
  font-size: 11;
  color: #333;
  align-items: center;
  justify-content: center;
  padding: 8px;
}

.player-name-input-button {
  position: unset;
  left: unset;
  transform: unset;
}

.player-name {
  text-align: center;
}

.player-name-input-button:active {
  transform: unset;
  box-shadow: unset;
}
</style>
