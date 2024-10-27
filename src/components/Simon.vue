<template>

    <div class="max-w-lg mx-auto">

        <!-- Panel para mostrar el progreso -->

        <div class="text-center mb-4">
            <h2 class="text-2xl font-bold">Nivel: {{ nivel }}</h2>
            <p>Puntuación: {{ puntuacion }}</p>
        </div>

        <!-- Grid de botones de colores -->

        <div class="grid grid-cols-3 gap-4 mb-4">

            <!-- Botones de colores mapeados segun el indice -->

            <button v-for="(color, indice) in colores" :key="indice" :class="[
                'h-24 rounded-lg transition-opacity transition-transform',
                color.clase,

                // Maneja el botón activo mediante opacidad

                { 'opacity-100 shadow-lg': colorActivo === indice, 'opacity-65': colorActivo !== indice }
            ]" @click="manejarClickJugador(indice)" :disabled="!turnoJugador || enJuego">
            </button>
        </div>

        <!-- Botón para iniciar/reiniciar -->

        <button @click="iniciarJuego" class="w-full py-2 bg-green-500 text-white rounded hover:bg-green-600"
            :disabled="enJuego">
            {{ juegoIniciado ? 'Reiniciar' : 'Comenzar' }}
        </button>
    </div>
</template>

<script>
export default {

    data() {
        return {

            // Array de colores disponibles, esto define la cantidad de botones en el mapeo del template

            colores: [
                { clase: 'bg-red-500' },
                { clase: 'bg-blue-500' },
                { clase: 'bg-yellow-500' },
                { clase: 'bg-green-500' },
                { clase: 'bg-purple-500' },
                { clase: 'bg-orange-500' },
                { clase: 'bg-pink-500' },
                { clase: 'bg-indigo-500' },
                { clase: 'bg-teal-500' }
            ],

            secuencia: [],        // Guarda la secuencia que el jugador debe repetir
            secuenciaJugador: [], // Guarda la secuencia que hace el jugador
            nivel: 1,             // Nivel actual del juego
            puntuacion: 0,        // Puntuación actual
            colorActivo: null,    // Índice del color que está activo actualmente
            turnoJugador: false,  // Indica si es el turno del jugador
            enJuego: false,       // Indica si hay una secuencia reproduciendo
            juegoIniciado: false  // Indica si el juego está en curso
        }
    },

    // Methods contiene la lógica del juego. Se usa un enfoque de programación orientada a objetos (POO).

    methods: {

        // Iniciar una nueva partida, reiniciando el estado y comienza la primera ronda

        async iniciarJuego() {
            this.reiniciarJuego()
            this.juegoIniciado = true
            await this.siguienteRonda()
        },

        // Restablece todas las variables

        reiniciarJuego() {
            this.secuencia = []
            this.secuenciaJugador = []
            this.nivel = 1
            this.puntuacion = 0
            this.turnoJugador = false
            this.enJuego = false
        },

        // Prepara y ejecuta la siguiente ronda del juego

        async siguienteRonda() {
            this.enJuego = true
            this.turnoJugador = false

            // Elige un color random y lo agrega a la secuencia que debera seguir el jugador

            this.secuencia.push(Math.floor(Math.random() * 4))
            await this.reproducirSecuencia()
            this.turnoJugador = true
            this.enJuego = false
        },

        // Reproduce la secuencia para el jugador

        async reproducirSecuencia() {
            for (let color of this.secuencia) {
                this.colorActivo = color
                await new Promise(resolve => setTimeout(resolve, 400)) // Tiempo que el color permanece activo
                this.colorActivo = null
                await new Promise(resolve => setTimeout(resolve, 150)) // Pausa entre colores
            }
        },

        // Evalua cada click en los botones

        async manejarClickJugador(indiceColor) {
            if (!this.turnoJugador) return

            // Agrega el color seleccionado a la variable que guarda la secuencia del jugador

            this.secuenciaJugador.push(indiceColor)

            // Y muestra la animación cuando el jugador hace click en los botones

            this.colorActivo = indiceColor
            await new Promise(resolve => setTimeout(resolve, 200))
            this.colorActivo = null

            // Verifica si el color seleccionado es correcto, sino termina el juego

            const indiceActual = this.secuenciaJugador.length - 1
            if (this.secuenciaJugador[indiceActual] !== this.secuencia[indiceActual]) {
                alert('Juego terminado, puntuación final: ' + this.puntuacion)
                this.juegoIniciado = false
                return
            }

            // Como verificamos que la secuencia que introdujo el jugador está correcta, 
            // Se genera la siguiente ronda llamando a siguinteRonda()

            if (this.secuenciaJugador.length === this.secuencia.length) {
                this.puntuacion += 10
                this.nivel++
                this.secuenciaJugador = []
                await new Promise(resolve => setTimeout(resolve, 1000)) // Es un simple delay
                await this.siguienteRonda()
            }
        }
    }
}
</script>