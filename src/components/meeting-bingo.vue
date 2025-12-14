<script setup>
/**
 * MeetingBingo Component
 * A 5x5 bingo card of meeting types
 * Perfect for playing during your next Afstemmingsoverleg
 */
import { ref, computed, onMounted } from 'vue'
import { meetingWords } from '../data/words.js'

// Bingo card state
const bingoCard = ref([])
const markedCells = ref(new Set())
const hasBingo = ref(false)
const winningCells = ref([])

/**
 * Generate a new 5x5 bingo card with random meeting words
 */
function generateCard() {
  // Shuffle and pick 25 unique words (24 + free space)
  const shuffled = [...meetingWords].sort(() => Math.random() - 0.5)
  const selected = shuffled.slice(0, 24)

  // Insert "GRATIS" (free space) in the middle
  selected.splice(12, 0, 'GRATIS\n🎉')

  bingoCard.value = selected
  markedCells.value = new Set([12]) // Free space is always marked
  hasBingo.value = false
  winningCells.value = []
}

/**
 * Toggle a cell's marked state
 */
function toggleCell(index) {
  if (index === 12) return // Can't unmark free space

  const newMarked = new Set(markedCells.value)
  if (newMarked.has(index)) {
    newMarked.delete(index)
  } else {
    newMarked.add(index)
  }
  markedCells.value = newMarked

  checkForBingo()
}

/**
 * Check all possible bingo lines
 */
function checkForBingo() {
  const winning = []

  // Check rows
  for (let row = 0; row < 5; row++) {
    const rowIndices = [0, 1, 2, 3, 4].map(col => row * 5 + col)
    if (rowIndices.every(i => markedCells.value.has(i))) {
      winning.push(...rowIndices)
    }
  }

  // Check columns
  for (let col = 0; col < 5; col++) {
    const colIndices = [0, 1, 2, 3, 4].map(row => row * 5 + col)
    if (colIndices.every(i => markedCells.value.has(i))) {
      winning.push(...colIndices)
    }
  }

  // Check diagonals
  const diag1 = [0, 6, 12, 18, 24]
  const diag2 = [4, 8, 12, 16, 20]

  if (diag1.every(i => markedCells.value.has(i))) {
    winning.push(...diag1)
  }
  if (diag2.every(i => markedCells.value.has(i))) {
    winning.push(...diag2)
  }

  winningCells.value = [...new Set(winning)]
  hasBingo.value = winning.length > 0
}

/**
 * Check if a cell is part of a winning line
 */
function isWinningCell(index) {
  return winningCells.value.includes(index)
}

// Generate initial card on mount
onMounted(() => {
  generateCard()
})
</script>

<template>
  <section class="py-12 px-4 bg-gradient-to-b from-gray-100 to-white">
    <div class="max-w-2xl mx-auto">
      <!-- Header -->
      <div class="text-center mb-8">
        <h2 class="text-3xl md:text-4xl font-bold font-display text-gray-900 mb-2">
          🎯 Meeting Bingo
        </h2>
        <p class="text-gray-600">
          Speel tijdens je volgende overleg! Klik wanneer je een woord hoort.
        </p>
      </div>

      <!-- Bingo Celebration -->
      <div
        v-if="hasBingo"
        class="mb-6 p-4 bg-gradient-to-r from-yellow-400 via-orange-500 to-red-500
               rounded-xl text-white text-center animate-celebrate shadow-lg"
      >
        <p class="text-4xl font-bold mb-2">🎉 BINGO! 🎉</p>
        <p class="text-lg">Je hebt gewonnen! (Net als iedereen in een meeting, niemand eigenlijk)</p>
      </div>

      <!-- Bingo Card -->
      <div class="bg-white rounded-2xl shadow-xl p-4 md:p-6">
        <!-- BINGO Header -->
        <div class="grid grid-cols-5 gap-1 mb-2">
          <div v-for="letter in ['B', 'I', 'N', 'G', 'O']" :key="letter"
               class="text-center text-2xl md:text-3xl font-bold text-blue-900 py-2">
            {{ letter }}
          </div>
        </div>

        <!-- Card Grid -->
        <div class="grid grid-cols-5 gap-1 md:gap-2">
          <button
            v-for="(word, index) in bingoCard"
            :key="index"
            @click="toggleCell(index)"
            class="aspect-square p-1 md:p-2 text-xs md:text-sm font-medium rounded-lg
                   transition-all duration-200 transform hover:scale-105
                   flex items-center justify-center text-center leading-tight"
            :class="{
              'bg-green-500 text-white shadow-lg': markedCells.has(index) && !isWinningCell(index),
              'bg-yellow-400 text-gray-900 shadow-lg animate-pulse': isWinningCell(index),
              'bg-gray-100 hover:bg-gray-200 text-gray-700': !markedCells.has(index),
              'cursor-default': index === 12
            }"
          >
            <span class="break-words hyphens-auto" :class="{ 'text-lg': index === 12 }">
              {{ word }}
            </span>
          </button>
        </div>
      </div>

      <!-- New Card Button -->
      <div class="text-center mt-6">
        <button
          @click="generateCard"
          class="px-6 py-3 bg-blue-600 hover:bg-blue-700 text-white font-semibold
                 rounded-lg shadow-md hover:shadow-lg transition-all duration-200
                 transform hover:scale-105 active:scale-95"
        >
          🔄 Nieuwe Kaart
        </button>
      </div>

      <!-- Instructions -->
      <div class="mt-6 p-4 bg-blue-50 rounded-lg text-sm text-blue-800">
        <p class="font-semibold mb-1">📋 Spelregels:</p>
        <ul class="list-disc list-inside space-y-1 text-blue-700">
          <li>Klik op een vakje wanneer je dat woord hoort in een meeting</li>
          <li>Het middelste vakje is gratis (net als de koffie)</li>
          <li>5 op een rij = BINGO!</li>
          <li>Prijs: nog een meeting om je winst te vieren</li>
        </ul>
      </div>
    </div>
  </section>
</template>
