<script setup>
/**
 * Praten - The Belastingdienst Dictionary
 * A satirical Vue app celebrating the Dutch tax office's
 * impressive 129+ words for "meeting/talking"
 *
 * Like Eskimos have 50 words for snow,
 * the Belastingdienst has 129+ words for "let's talk"
 */
import { ref } from 'vue'
import MeetingGenerator from './components/meeting-generator.vue'
import MeetingBingo from './components/meeting-bingo.vue'
import StatsSection from './components/stats-section.vue'
import { meetingWords } from './data/words.js'

// Total word count for hero display
const totalWords = ref(meetingWords.length)
</script>

<template>
  <div class="min-h-screen bg-gray-50 font-body">
    <!-- Hero Section -->
    <header class="bg-gradient-to-br from-blue-900 via-blue-800 to-indigo-900 text-white py-16 md:py-24 px-4">
      <div class="max-w-4xl mx-auto text-center">
        <!-- Badge -->
        <div class="inline-flex items-center gap-2 bg-white/10 backdrop-blur px-4 py-2 rounded-full text-sm mb-6">
          <span class="animate-pulse">🔴</span>
          <span>OFFICIEEL ONOFFICIEEL</span>
        </div>

        <!-- Main Title -->
        <h1 class="text-4xl md:text-6xl lg:text-7xl font-bold font-display mb-4 leading-tight">
          Het Belastingdienst<br>
          <span class="text-orange-400">Woordenboek</span>
        </h1>

        <!-- Subtitle -->
        <p class="text-xl md:text-2xl text-blue-200 mb-8 max-w-2xl mx-auto">
          Eskimo's hebben ~50 woorden voor sneeuw.<br>
          De Belastingdienst heeft...
        </p>

        <!-- Big Number -->
        <div class="inline-block bg-white/10 backdrop-blur rounded-2xl px-8 py-6 mb-8">
          <p class="text-6xl md:text-8xl font-bold text-orange-400 font-display">
            {{ totalWords }}
          </p>
          <p class="text-xl text-blue-200 mt-2">
            woorden voor "praten" 🗣️
          </p>
        </div>

        <!-- Scroll hint -->
        <div class="animate-bounce mt-4">
          <svg class="w-8 h-8 mx-auto text-white/50" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 14l-7 7m0 0l-7-7m7 7V3"/>
          </svg>
        </div>
      </div>
    </header>

    <!-- Quick Samples -->
    <section class="bg-orange-500 text-white py-4 overflow-hidden">
      <div class="flex animate-marquee whitespace-nowrap">
        <span v-for="(word, i) in meetingWords.slice(0, 20)" :key="i" class="mx-4 text-sm font-medium">
          {{ word }} •
        </span>
        <span v-for="(word, i) in meetingWords.slice(0, 20)" :key="'dup-'+i" class="mx-4 text-sm font-medium">
          {{ word }} •
        </span>
      </div>
    </section>

    <!-- Meeting Generator -->
    <MeetingGenerator />

    <!-- Divider -->
    <div class="h-px bg-gradient-to-r from-transparent via-gray-300 to-transparent"></div>

    <!-- Meeting Bingo -->
    <MeetingBingo />

    <!-- Statistics -->
    <StatsSection />

    <!-- Footer -->
    <footer class="bg-gray-900 text-gray-400 py-8 px-4">
      <div class="max-w-4xl mx-auto text-center">
        <p class="mb-2">
          🏛️ Niet gelieerd aan de Belastingdienst
        </p>
        <p class="text-sm">
          Gemaakt met ❤️ en veel te veel ☕ | {{ new Date().getFullYear() }}
        </p>
        <p class="text-xs mt-4 text-gray-600">
          Disclaimer: Dit is satire. Wij houden van de Belastingdienst.*<br>
          <span class="text-gray-700">*Zolang ze dit niet lezen.</span>
        </p>
      </div>
    </footer>
  </div>
</template>

<style>
/* Marquee animation for scrolling words */
@keyframes marquee {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(-50%);
  }
}

.animate-marquee {
  animation: marquee 30s linear infinite;
}

.animate-marquee:hover {
  animation-play-state: paused;
}
</style>
