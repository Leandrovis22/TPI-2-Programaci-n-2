<template>

  <div class="max-w-2xl mx-auto">

    <div class="text-center mb-4">
      <h2 class="text-2xl font-bold">Pregunta {{ preguntaActual + 1 }} de {{ preguntas.length }}</h2>
      <p class="text-lg">Puntuación: {{ puntaje }}</p>
      <p v-if="!juegoTerminado" class="text-sm text-gray-600">Respuestas correctas: {{ respuestasCorrectas }} de {{ totalRespondidas }}</p>
    </div>

    <div v-if="!juegoTerminado" class="bg-white p-6 rounded-lg shadow-md">

      <!-- Pregunta actual -->

      <h3 class="text-xl mb-4">{{ datosPreguntaActual.pregunta }}</h3>
      
      <!-- Lista de respuestas que el jugador puede elegir -->

      <div class="space-y-2">
        <button
          v-for="(respuesta, indice) in datosPreguntaActual.respuestas"
          :key="indice"
          @click="verificarRespuesta(indice)"
          :disabled="respondido"
          :class="[
            'w-full p-3 text-left rounded transition-colors duration-200',
            'disabled:opacity-70 disabled:cursor-not-allowed',

            // Colores según el estado de la respuesta

            respondido && indice === datosPreguntaActual.correcto ? 'bg-green-500 text-white' :
            respondido && respuestaSeleccionada === indice ? 'bg-red-500 text-white' :
            'bg-gray-100 hover:bg-gray-200'
          ]"
        >
          {{ respuesta }}
        </button>
      </div>

      <!-- Botón para avanzar a la siguiente pregunta -->

      <div class="mt-4">
        <button
          v-if="respondido"
          @click="siguientePregunta"
          class="w-full py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
        >
          {{ esUltimaPregunta ? 'Ver resultados finales' : 'Siguiente pregunta' }}
        </button>
      </div>
    </div>

    <!-- Resultados finales (se muestra este div una vez que "juegoTerminado" sea true)-->

    <div v-else class="bg-white p-6 rounded-lg shadow-md">
      <h3 class="text-2xl font-bold mb-4 text-center">Juego terminado!</h3>
      
      <!-- Resumen de resultados -->

      <div class="space-y-4">
        <div class="text-center">
          <p class="text-2xl font-bold mb-4 text-center pt-4">
            Respuestas correctas: {{ respuestasCorrectas }} de {{ preguntas.length }} ({{ (respuestasCorrectas / preguntas.length * 100).toFixed(1) }}%)
          </p>
        </div>

        <!-- Barra de progreso -->

        <div class="h-4 bg-gray-200 rounded-full overflow-hidden">
          <div class="h-full transition-all duration-500"
              
              :class="[respuestasCorrectas / preguntas.length >= 0.8 ? 'bg-green-500' :
              respuestasCorrectas / preguntas.length >= 0.6 ? 'bg-yellow-500' : 'bg-red-500']"

              :style="{ width: `${(respuestasCorrectas / preguntas.length * 100)}%` }"

          ></div>
        </div>

        <!-- Botón para reiniciar el juego -->

        <button @click="reiniciarJuego" class="w-full py-2 bg-green-500 text-white rounded hover:bg-green-600">
          Reiniciar
        </button>
      </div>
    </div>

  </div>
</template>

<script>

// Importamos las preguntas del json (50 preguntas con sus 3 respuestas c/una)

import Data from '../assets/data/preguntas-trivia.json'

export default {

  // Estado inicial del juego

  data() {
    return {
      preguntas: [],          // Array de preguntas
      preguntaActual: 0,      // Índice de la pregunta actual
      puntaje: 0,             // Puntuación acumulada
      respondido: false,      // Indica si la pregunta actual fue respondida
      respuestaSeleccionada: null,  // Índice de la respuesta seleccionada
      respuestasCorrectas: 0, // Contador de respuestas correctas
      totalRespondidas: 0,    // Total de preguntas respondidas
      juegoTerminado: false   // Indica si el juego terminó
    }
  },

  // Se ejecuta al crear el componente

  created() {
    this.iniciarJuego()
  },

  // Propiedades computadas (sirven para renderizar elementos dinámicos) 

  computed: {

    // Obtiene los datos de la pregunta actual

    datosPreguntaActual() {
      const pregunta = this.preguntas[this.preguntaActual]
      return {
        pregunta: pregunta.pregunta,
        respuestas: pregunta.respuestas,
        correcto: pregunta.correcto
      }
    },

    // Verifica si es la última pregunta, retorna un verdadero o falso

    esUltimaPregunta() {
      return this.preguntaActual === this.preguntas.length - 1
    }
  },

  methods: {

    // Inicializa o reinicia el juego

    iniciarJuego() {

      // Selecciona 10 preguntas aleatorias del json de preguntas

      this.preguntas = [...Data.preguntas].sort(() => Math.random() - 0.5).slice(0, 10)
      
      // Reinicia todas las variables del juego

      this.preguntaActual = 0
      this.puntaje = 0
      this.respondido = false
      this.respuestaSeleccionada = null
      this.respuestasCorrectas = 0
      this.totalRespondidas = 0
      this.juegoTerminado = false
    },

    // Verifica si la respuesta seleccionada es correcta

    verificarRespuesta(indiceSeleccionado) {
      if (this.respondido) return
      
      this.respondido = true
      this.respuestaSeleccionada = indiceSeleccionado
      this.totalRespondidas++
      
      // Suma puntos si la respuesta es correcta

      if (indiceSeleccionado === this.datosPreguntaActual.correcto) {
        this.puntaje += 10
        this.respuestasCorrectas++
      }
    },

    // Avanza a la siguiente pregunta o finaliza el juego

    siguientePregunta() {
      if (this.esUltimaPregunta) {
        this.juegoTerminado = true
        return
      }
      
      this.preguntaActual++
      this.respondido = false
      this.respuestaSeleccionada = null
    },

    // Reinicia el juego

    reiniciarJuego() {
      this.iniciarJuego()
    }
  }
}
</script>