<template>
    <div class="max-w-2xl mx-auto">
      <div class="text-center mb-4">
        <h2 class="text-2xl font-bold">Pregunta {{ preguntaActual + 1 }} de {{ preguntas.length }}</h2>
        <p class="text-lg">Puntuación: {{ puntaje }}</p>
        <p class="text-sm text-gray-600">Respuestas correctas: {{ respuestasCorrectas }} de {{ totalRespondidas }}</p>
      </div>
  
      <div v-if="!juegoTerminado" class="bg-white p-6 rounded-lg shadow-md">
        <h3 class="text-xl mb-4">{{ datosPreguntaActual.pregunta }}</h3>
        
        <div class="space-y-2">
          <button
            v-for="(respuesta, indice) in datosPreguntaActual.respuestas"
            :key="indice"
            @click="verificarRespuesta(indice)"
            :disabled="respondido"
            class="w-full p-3 text-left rounded transition-colors duration-200"
            :class="[
              respondido && indice === datosPreguntaActual.correcto
                ? 'bg-green-500 text-white'
                : respondido && respuestaSeleccionada === indice
                ? 'bg-red-500 text-white'
                : 'bg-gray-100 hover:bg-gray-200'
            ]"
          >
            {{ respuesta }}
          </button>
        </div>
  
        <div class="mt-4">
          <button
            v-if="respondido"
            @click="siguientePregunta"
            class="w-full py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors duration-200"
          >
            {{ esUltimaPregunta ? 'Ver resultados finales' : 'Siguiente pregunta' }}
          </button>
        </div>
      </div>
  
      <div v-else class="bg-white p-6 rounded-lg shadow-md">
        <h3 class="text-2xl font-bold mb-4 text-center">¡Juego terminado!</h3>
        
        <div class="space-y-4">
          <div class="text-center">
            <p class="text-lg">Puntuación final: {{ puntaje }}</p>
            <p class="text-md">Respuestas correctas: {{ respuestasCorrectas }} de {{ preguntas.length }}</p>
            <p class="text-md">Porcentaje de aciertos: {{ (respuestasCorrectas / preguntas.length * 100).toFixed(1) }}%</p>
          </div>
  
          <div class="space-y-2">
            <p class="font-semibold">Tu rendimiento:</p>
            <div class="h-4 bg-gray-200 rounded-full overflow-hidden">
              <div 
                class="h-full transition-all duration-500"
                :class="[
                  respuestasCorrectas / preguntas.length >= 0.8 ? 'bg-green-500' :
                  respuestasCorrectas / preguntas.length >= 0.6 ? 'bg-yellow-500' : 'bg-red-500'
                ]"
                :style="{ width: `${(respuestasCorrectas / preguntas.length * 100)}%` }"
              ></div>
            </div>
            <p class="text-sm text-gray-600 text-center">
              {{ obtenerMensajeRendimiento() }}
            </p>
          </div>
  
          <button
            @click="reiniciarJuego"
            class="w-full py-2 bg-green-500 text-white rounded hover:bg-green-600 transition-colors duration-200"
          >
            Jugar de nuevo
          </button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import triviaData from '../assets/data/preguntas-trivia.json'
  
  export default {
    data() {
      return {
        preguntas: [],
        preguntaActual: 0,
        puntaje: 0,
        respondido: false,
        respuestaSeleccionada: null,
        respuestasCorrectas: 0,
        totalRespondidas: 0,
        juegoTerminado: false,
        temporizadorPregunta: null,
        tiempoPorPregunta: 20,
        tiempoRestante: 20
      }
    },
    created() {
      this.iniciarJuego()
    },
    computed: {
      datosPreguntaActual() {
        const datosPregunta = this.preguntas[this.preguntaActual]
        return {
          pregunta: datosPregunta.pregunta,
          respuestas: datosPregunta.respuestas,
          correcto: datosPregunta.correcto
        }
      },
      esUltimaPregunta() {
        return this.preguntaActual === this.preguntas.length - 1
      }
    },
    methods: {
      iniciarJuego() {
        this.preguntas = [...triviaData.preguntas]
          .sort(() => Math.random() - 0.5)
          .slice(0, 10)
        
        this.preguntaActual = 0
        this.puntaje = 0
        this.respondido = false
        this.respuestaSeleccionada = null
        this.respuestasCorrectas = 0
        this.totalRespondidas = 0
        this.juegoTerminado = false
        this.tiempoRestante = this.tiempoPorPregunta
      },
      verificarRespuesta(indiceSeleccionado) {
        if (this.respondido) return
  
        this.respondido = true
        this.respuestaSeleccionada = indiceSeleccionado
        this.totalRespondidas++
        
        if (indiceSeleccionado === this.datosPreguntaActual.correcto) {
          this.puntaje += 10
          this.respuestasCorrectas++
        }
      },
      siguientePregunta() {
        if (this.esUltimaPregunta) {
          this.juegoTerminado = true
          return
        }
        
        this.preguntaActual++
        this.respondido = false
        this.respuestaSeleccionada = null
        this.tiempoRestante = this.tiempoPorPregunta
      },
      reiniciarJuego() {
        this.iniciarJuego()
      },
      obtenerMensajeRendimiento() {
        const porcentaje = this.respuestasCorrectas / this.preguntas.length
        if (porcentaje >= 0.8) {
          return '¡Excelente! ¡Eres un experto!'
        } else if (porcentaje >= 0.6) {
          return '¡Buen trabajo! Sigue practicando.'
        } else {
          return 'Puedes mejorar. ¡Inténtalo de nuevo!'
        }
      },
      formatearTiempo(segundos) {
        return `${Math.floor(segundos / 60)}:${(segundos % 60).toString().padStart(2, '0')}`
      }
    },
    watch: {
      juegoTerminado(nuevoValor) {
        if (nuevoValor && this.temporizadorPregunta) {
          clearInterval(this.temporizadorPregunta)
        }
      }
    },
    beforeUnmount() {
      if (this.temporizadorPregunta) {
        clearInterval(this.temporizadorPregunta)
      }
    }
  }
  </script>
  
  <style scoped>
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.3s;
  }
  
  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
  }
  
  button:disabled {
    cursor: not-allowed;
    opacity: 0.7;
  }
  </style>
  