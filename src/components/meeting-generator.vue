<script setup>
/**
 * MeetingGenerator Component
 * The heart of the app - generates random Dutch tax office meeting types
 * with hilariously bureaucratic stats
 */
import { ref, computed } from 'vue'
import { meetingWords, getDefinition } from '../data/words.js'

// Current meeting state
const currentWord = ref(null)
const isGenerating = ref(false)
const showResult = ref(false)

/**
 * Generate random stats for a meeting
 * Because every meeting needs KPIs
 */
const meetingStats = computed(() => {
  if (!currentWord.value) return null

  // Seed random based on word for consistent stats per word
  const seed = currentWord.value.length + currentWord.value.charCodeAt(0)

  return {
    // Duration: more syllables = longer meeting (obviously)
    duration: calculateDuration(currentWord.value),
    // Coffee cups needed to survive
    coffeeCups: Math.floor((seed % 5) + 2),
    // Bureaucracy level in stamps (1-5)
    bureaucracyLevel: Math.floor((seed % 5) + 1),
    // Attendees: overleg = more people
    attendees: calculateAttendees(currentWord.value),
    // Chance of actually deciding something
    decisionChance: Math.floor(Math.random() * 15) + 1
  }
})

/**
 * Calculate meeting duration based on word complexity
 */
function calculateDuration(word) {
  const baseMinutes = 30
  const syllableMultiplier = countSyllables(word) * 15
  const overlegBonus = word.toLowerCase().includes('overleg') ? 45 : 0
  const vergaderingBonus = word.toLowerCase().includes('vergadering') ? 60 : 0

  const totalMinutes = baseMinutes + syllableMultiplier + overlegBonus + vergaderingBonus

  const hours = Math.floor(totalMinutes / 60)
  const minutes = totalMinutes % 60

  if (hours >= 1) {
    return `${hours}u ${minutes}min`
  }
  return `${minutes} min`
}

/**
 * Rough syllable counter for Dutch words
 */
function countSyllables(word) {
  const vowels = word.match(/[aeiouàèéëïöü]/gi)
  return vowels ? Math.max(vowels.length, 1) : 1
}

/**
 * Calculate attendees based on word type
 */
function calculateAttendees(word) {
  const lowerWord = word.toLowerCase()

  if (lowerWord.includes('groot') || lowerWord.includes('directie')) return '15-25'
  if (lowerWord.includes('team') || lowerWord.includes('cluster')) return '8-12'
  if (lowerWord.includes('drie')) return '3'
  if (lowerWord.includes('persoonlijk') || lowerWord.includes('gesprek')) return '2-3'
  if (lowerWord.includes('coaching') || lowerWord.includes('interview')) return '2'

  return '4-8'
}

/**
 * Generate a new random meeting
 * Includes dramatic animation delay
 */
function generateMeeting() {
  isGenerating.value = true
  showResult.value = false

  // Dramatic shuffle effect
  let shuffleCount = 0
  const shuffleInterval = setInterval(() => {
    currentWord.value = meetingWords[Math.floor(Math.random() * meetingWords.length)]
    shuffleCount++

    if (shuffleCount >= 15) {
      clearInterval(shuffleInterval)
      isGenerating.value = false
      showResult.value = true
    }
  }, 80)
}

// Get definition for current word
const currentDefinition = computed(() => {
  return currentWord.value ? getDefinition(currentWord.value) : ''
})
</script>

<template>
  <section class="py-12 px-4">
    <div class="max-w-2xl mx-auto text-center">
      <!-- Generator Button -->
      <button
        @click="generateMeeting"
        :disabled="isGenerating"
        class="group relative px-8 py-4 bg-orange-500 hover:bg-orange-600 disabled:bg-orange-400
               text-white text-xl font-bold rounded-lg shadow-lg hover:shadow-xl
               transform hover:scale-105 active:scale-95 transition-all duration-200
               disabled:cursor-not-allowed disabled:transform-none"
      >
        <span v-if="!isGenerating">
          🎰 Genereer Je Volgende Meeting
        </span>
        <span v-else class="flex items-center gap-2">
          <svg class="animate-spin h-5 w-5" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" fill="none"/>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"/>
          </svg>
          Shuffelen...
        </span>
      </button>

      <!-- Result Card -->
      <div
        v-if="currentWord"
        class="mt-8 bg-white rounded-2xl shadow-2xl overflow-hidden transform transition-all duration-500"
        :class="{ 'animate-flip': showResult }"
      >
        <!-- Header with meeting name -->
        <div class="bg-gradient-to-r from-blue-900 to-blue-700 p-6 text-white">
          <p class="text-sm uppercase tracking-wider opacity-75 mb-2">Uw meeting type is:</p>
          <h2 class="text-3xl md:text-4xl font-bold font-display">
            {{ currentWord }}
          </h2>
        </div>

        <!-- Definition -->
        <div class="p-6 bg-gray-50 border-b">
          <p class="text-gray-600 italic text-lg">
            "{{ currentDefinition }}"
          </p>
        </div>

        <!-- Stats Grid -->
        <div v-if="meetingStats" class="p-6 grid grid-cols-2 md:grid-cols-4 gap-4">
          <!-- Duration -->
          <div class="text-center p-4 bg-blue-50 rounded-lg">
            <div class="text-3xl mb-1">⏱️</div>
            <div class="text-2xl font-bold text-blue-900">{{ meetingStats.duration }}</div>
            <div class="text-xs text-gray-500 uppercase">Geschatte duur</div>
          </div>

          <!-- Coffee -->
          <div class="text-center p-4 bg-amber-50 rounded-lg">
            <div class="text-3xl mb-1">
              <span v-for="n in meetingStats.coffeeCups" :key="n">☕</span>
            </div>
            <div class="text-2xl font-bold text-amber-900">{{ meetingStats.coffeeCups }}</div>
            <div class="text-xs text-gray-500 uppercase">Koffie nodig</div>
          </div>

          <!-- Bureaucracy Level -->
          <div class="text-center p-4 bg-red-50 rounded-lg">
            <div class="text-3xl mb-1">
              <span v-for="n in meetingStats.bureaucracyLevel" :key="n" class="inline-block" :style="{ animationDelay: `${n * 0.1}s` }">📋</span>
            </div>
            <div class="text-2xl font-bold text-red-900">{{ meetingStats.bureaucracyLevel }}/5</div>
            <div class="text-xs text-gray-500 uppercase">Bureaucratie</div>
          </div>

          <!-- Attendees -->
          <div class="text-center p-4 bg-green-50 rounded-lg">
            <div class="text-3xl mb-1">👥</div>
            <div class="text-2xl font-bold text-green-900">{{ meetingStats.attendees }}</div>
            <div class="text-xs text-gray-500 uppercase">Deelnemers</div>
          </div>
        </div>

        <!-- Decision Chance - Full Width -->
        <div v-if="meetingStats" class="px-6 pb-6">
          <div class="bg-gray-100 rounded-lg p-4">
            <div class="flex items-center justify-between mb-2">
              <span class="text-sm text-gray-600">Kans op daadwerkelijke beslissing:</span>
              <span class="text-lg font-bold text-red-600">{{ meetingStats.decisionChance }}%</span>
            </div>
            <div class="w-full bg-gray-300 rounded-full h-3">
              <div
                class="bg-red-500 h-3 rounded-full transition-all duration-1000"
                :style="{ width: `${meetingStats.decisionChance}%` }"
              ></div>
            </div>
          </div>
        </div>
      </div>

      <!-- Initial State -->
      <div v-else class="mt-8 p-12 border-2 border-dashed border-gray-300 rounded-2xl">
        <p class="text-gray-400 text-lg">
          Klik op de knop om je bureaucratische lot te ontdekken...
        </p>
      </div>
    </div>
  </section>
</template>
