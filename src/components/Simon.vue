<template>
    <div class="max-w-lg mx-auto">
        <div class="text-center mb-4">
            <h2 class="text-2xl font-bold">Nivel: {{ nivel }}</h2>
            <p>Puntuación: {{ puntuacion }}</p>
        </div>

        <div class="grid grid-cols-3 gap-4 mb-4">
            <button v-for="(color, indice) in colores" :key="indice" :class="[
                'h-24 rounded-lg transition-opacity transition-transform',
                color.clase,
                { 'opacity-100 shadow-lg': colorActivo === indice, 'opacity-65': colorActivo !== indice }
            ]" @click="manejarClickJugador(indice)" :disabled="!turnoJugador || enJuego"></button>

        </div>

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

            secuencia: [],
            secuenciaJugador: [],
            nivel: 1,
            puntuacion: 0,
            colorActivo: null,
            turnoJugador: false,
            enJuego: false,
            juegoIniciado: false
        }
    },
    methods: {
        async iniciarJuego() {
            this.reiniciarJuego()
            this.juegoIniciado = true
            await this.siguienteRonda()
        },
        reiniciarJuego() {
            this.secuencia = []
            this.secuenciaJugador = []
            this.nivel = 1
            this.puntuacion = 0
            this.turnoJugador = false
            this.enJuego = false
        },
        async siguienteRonda() {
            this.enJuego = true
            this.turnoJugador = false
            this.secuencia.push(Math.floor(Math.random() * 4))
            await this.reproducirSecuencia()
            this.turnoJugador = true
            this.enJuego = false
        },
        async reproducirSecuencia() {
            for (let color of this.secuencia) {
                this.colorActivo = color
                await new Promise(resolve => setTimeout(resolve, 500))
                this.colorActivo = null
                await new Promise(resolve => setTimeout(resolve, 200))
            }
        },
        async manejarClickJugador(indiceColor) {
            if (!this.turnoJugador) return

            this.secuenciaJugador.push(indiceColor)
            this.colorActivo = indiceColor
            await new Promise(resolve => setTimeout(resolve, 200))
            this.colorActivo = null

            const indiceActual = this.secuenciaJugador.length - 1
            if (this.secuenciaJugador[indiceActual] !== this.secuencia[indiceActual]) {
                alert('¡Juego terminado! Puntuación final: ' + this.puntuacion)
                this.juegoIniciado = false
                return
            }

            if (this.secuenciaJugador.length === this.secuencia.length) {
                this.puntuacion += 10
                this.nivel++
                this.secuenciaJugador = []
                await new Promise(resolve => setTimeout(resolve, 1000))
                await this.siguienteRonda()
            }
        }
    }
}
</script>
