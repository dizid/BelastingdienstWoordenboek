<script setup>
/**
 * StatsSection Component
 * Fun statistics about the meeting word collection
 * Because bureaucracy loves metrics
 */
import { computed } from 'vue'
import { meetingWords, countWordsContaining } from '../data/words.js'

/**
 * Computed statistics about the word list
 */
const stats = computed(() => {
  // Count various patterns
  const overlegCount = countWordsContaining('overleg')
  const gesprekCount = countWordsContaining('gesprek')
  const sessieCount = countWordsContaining('sessie')
  const vergaderingCount = countWordsContaining('vergader')
  const bijeenkomstCount = countWordsContaining('bijeenkomst')

  // Calculate average word length
  const avgLength = (meetingWords.reduce((sum, w) => sum + w.length, 0) / meetingWords.length).toFixed(1)

  // Count words with spaces (compound terms)
  const compoundWords = meetingWords.filter(w => w.includes(' ')).length

  // Longest word
  const longestWord = meetingWords.reduce((a, b) => a.length > b.length ? a : b)

  // Shortest word
  const shortestWord = meetingWords.reduce((a, b) => a.length < b.length ? a : b)

  return {
    total: meetingWords.length,
    overlegCount,
    gesprekCount,
    sessieCount,
    vergaderingCount,
    bijeenkomstCount,
    avgLength,
    compoundWords,
    longestWord,
    shortestWord
  }
})

/**
 * Eskimo snow words comparison data
 */
const eskimoComparison = {
  eskimoWords: 50, // Common claim
  dutchTaxWords: meetingWords.length,
  ratio: (meetingWords.length / 50).toFixed(1)
}
</script>

<template>
  <section class="py-12 px-4 bg-blue-900 text-white">
    <div class="max-w-4xl mx-auto">
      <!-- Header -->
      <div class="text-center mb-10">
        <h2 class="text-3xl md:text-4xl font-bold font-display mb-4">
          📊 De Statistieken
        </h2>
        <p class="text-blue-200 text-lg">
          Een wetenschappelijke analyse van bureaucratisch taalgebruik
        </p>
      </div>

      <!-- Eskimo Comparison - Hero Stat -->
      <div class="bg-gradient-to-r from-blue-800 to-indigo-800 rounded-2xl p-6 md:p-8 mb-8 text-center">
        <p class="text-blue-200 text-sm uppercase tracking-wider mb-4">
          De beroemde vergelijking
        </p>
        <div class="flex flex-col md:flex-row items-center justify-center gap-4 md:gap-8">
          <div class="text-center">
            <p class="text-5xl md:text-6xl font-bold">🏔️ ~50</p>
            <p class="text-blue-300 mt-2">Eskimo woorden voor sneeuw</p>
          </div>
          <div class="text-4xl">VS</div>
          <div class="text-center">
            <p class="text-5xl md:text-6xl font-bold text-yellow-400">🏛️ {{ stats.total }}</p>
            <p class="text-blue-300 mt-2">Belastingdienst woorden voor praten</p>
          </div>
        </div>
        <p class="mt-6 text-xl text-yellow-300 font-semibold">
          {{ eskimoComparison.ratio }}x meer! 🏆
        </p>
      </div>

      <!-- Stats Grid -->
      <div class="grid grid-cols-2 md:grid-cols-3 gap-4">
        <!-- Overleg Count -->
        <div class="bg-blue-800/50 rounded-xl p-5 text-center">
          <p class="text-4xl font-bold text-orange-400">{{ stats.overlegCount }}</p>
          <p class="text-blue-200 text-sm mt-1">woorden met "overleg"</p>
          <p class="text-blue-400 text-xs mt-2">{{ Math.round(stats.overlegCount / stats.total * 100) }}% van totaal</p>
        </div>

        <!-- Gesprek Count -->
        <div class="bg-blue-800/50 rounded-xl p-5 text-center">
          <p class="text-4xl font-bold text-green-400">{{ stats.gesprekCount }}</p>
          <p class="text-blue-200 text-sm mt-1">woorden met "gesprek"</p>
          <p class="text-blue-400 text-xs mt-2">{{ Math.round(stats.gesprekCount / stats.total * 100) }}% van totaal</p>
        </div>

        <!-- Sessie Count -->
        <div class="bg-blue-800/50 rounded-xl p-5 text-center">
          <p class="text-4xl font-bold text-purple-400">{{ stats.sessieCount }}</p>
          <p class="text-blue-200 text-sm mt-1">woorden met "sessie"</p>
          <p class="text-blue-400 text-xs mt-2">{{ Math.round(stats.sessieCount / stats.total * 100) }}% van totaal</p>
        </div>

        <!-- Vergadering Count -->
        <div class="bg-blue-800/50 rounded-xl p-5 text-center">
          <p class="text-4xl font-bold text-pink-400">{{ stats.vergaderingCount }}</p>
          <p class="text-blue-200 text-sm mt-1">woorden met "vergader"</p>
        </div>

        <!-- Average Length -->
        <div class="bg-blue-800/50 rounded-xl p-5 text-center">
          <p class="text-4xl font-bold text-cyan-400">{{ stats.avgLength }}</p>
          <p class="text-blue-200 text-sm mt-1">gemiddelde woordlengte</p>
        </div>

        <!-- Compound Words -->
        <div class="bg-blue-800/50 rounded-xl p-5 text-center">
          <p class="text-4xl font-bold text-yellow-400">{{ stats.compoundWords }}</p>
          <p class="text-blue-200 text-sm mt-1">samengestelde termen</p>
        </div>
      </div>

      <!-- Fun Facts -->
      <div class="mt-8 grid md:grid-cols-2 gap-4">
        <div class="bg-gradient-to-r from-green-600 to-green-700 rounded-xl p-5">
          <p class="text-green-200 text-sm uppercase tracking-wider mb-2">🏆 Langste woord</p>
          <p class="text-xl font-bold">{{ stats.longestWord }}</p>
          <p class="text-green-300 text-sm mt-1">{{ stats.longestWord.length }} karakters van pure bureaucratie</p>
        </div>

        <div class="bg-gradient-to-r from-red-600 to-red-700 rounded-xl p-5">
          <p class="text-red-200 text-sm uppercase tracking-wider mb-2">🥇 Kortste woord</p>
          <p class="text-xl font-bold">{{ stats.shortestWord }}</p>
          <p class="text-red-300 text-sm mt-1">Simpel. Direct. Ongebruikelijk.</p>
        </div>
      </div>

      <!-- Bottom Quote -->
      <div class="mt-10 text-center">
        <blockquote class="text-xl md:text-2xl italic text-blue-200">
          "Waar twee Nederlanders samenkomen, ontstaat een overleg.<br>
          Waar drie samenkomen, een afstemmingsoverleg."
        </blockquote>
        <p class="text-blue-400 mt-3">— Oud Belastingdienst Spreekwoord (waarschijnlijk)</p>
      </div>
    </div>
  </section>
</template>
