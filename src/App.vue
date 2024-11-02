<template>
  <div class="max-w-4xl mx-auto p-4">
    <h1 class="text-3xl font-bold mb-6 text-center mt-6">Vue Mini-Juegos</h1>

    <div class="flex gap-4 mb-6 justify-center">
      <button 
        @click="juegoActual = 'simon'"
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        Simon
      </button>
      <button 
        @click="juegoActual = 'memoria'"
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        Memoria
      </button>
      <button 
        @click="juegoActual = 'trivia'"
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        Trivia
      </button>
    </div>

    <component :is="componenteActual"></component>
  </div>
</template>

<script>
import Simon from './components/Simon.vue'
import Memoria from './components/Memoria.vue'
import Trivia from './components/Trivia.vue'

export default {
  components: {
    Simon,
    Memoria,
    Trivia
  },
  data() {
    return {
      juegoActual: 'simon'
    }
  },
  computed: {
    componenteActual() {
      const componentes = {
        simon: Simon,
        memoria: Memoria,
        trivia: Trivia
      }
      return componentes[this.juegoActual]
    }
  }
}
</script>
