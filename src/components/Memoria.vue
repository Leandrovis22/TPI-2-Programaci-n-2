<template>

    <div class="max-w-2xl mx-auto">

        <!-- Panel para mostrar el progreso actual -->

        <div class="text-center mb-4">
            <h2 class="text-2xl font-bold">Intentos: {{ intentos }}</h2>
            <p>Pares encontrados: {{ paresEncontrados.length / 2 }}</p>
        </div>

        <!-- Grid de cartas -->

        <div class="grid grid-cols-6 gap-4">

            <!-- Se hace una iteración para cada carta -->

            <div v-for="(carta, indice) in cartas" :key="indice" @click="mostrarCarta(indice)" class="aspect-square">

                <!-- Contenedor de carta individual -->

                <div class="w-full h-full transition-transform duration-500" :class="[
                    'rounded-lg border-2',
                    carta.visible || paresEncontrados.includes(indice)
                        ? 'bg-white'
                        : 'bg-blue-500'
                ]">

                    <!-- Emoji que se muestra cuando la carta está dada vuelta temporalmente, o fue encontrada exitosamente -->

                    <span v-if="carta.visible || paresEncontrados.includes(indice)"
                        class="flex items-center justify-center h-full text-5xl">
                        {{ carta.emoji }}
                    </span>
                </div>
            </div>
        </div>

        <!-- Botón para reiniciar el juego -->

        <button @click="reiniciarJuego" class="w-full mt-4 py-2 bg-green-500 text-white rounded hover:bg-green-600">
            Reiniciar
        </button>
    </div>
</template>

<script>
export default {

    // Estado inicial del juego

    data() {
        return {
            cartas: [],           // Array de objetos carta con propiedades emoji y visible
            cartasVisibles: [],   // Índices de las cartas actualmente activas (máximo 2)
            paresEncontrados: [], // Índices de las cartas que ya fueron encontradas en par
            intentos: 0,          // Contador de intentos
            emojis: ['🐶', '🐱', '🐲', '🐹', '🐰', '🦊', '🐻', '🐼', '🐎']
        }
    },

    // Inicia el juego cuando el componente se crea

    created() {
        this.iniciarJuego()
    },

    // Methods define las funciones que manejan la lógica. Se usa un enfoque programacion orientada a objetos (POO)

    methods: {

        // Se mezclan y duplican los emojis

        iniciarJuego() {
            this.cartas = [...this.emojis, ...this.emojis]
                .map((emoji) => ({
                    emoji,
                    visible: false
                }))
                .sort(() => Math.random() - 0.5)
        },

        // Control de lógica para mostrar las cartas y ver coincidencias

        async mostrarCarta(indice) {

            // Se evitan acciones como seleccionar más de 2 cartas o cartas ya encontradas

            if (
                this.cartasVisibles.length === 2 ||
                this.paresEncontrados.includes(indice) ||
                this.cartasVisibles.includes(indice)
            ) {
                return
            }

            // Selecciona la carta y la agrega al array de cartas visibles (solo hay 2 a la vez)

            this.cartas[indice].visible = true
            this.cartasVisibles.push(indice)

            // Lógica para cuando se seleccionaron dos cartas

            if (this.cartasVisibles.length === 2) {
                this.intentos++
                const [primerIndice, segundoIndice] = this.cartasVisibles

                // Verifica si las cartas coinciden

                if (this.cartas[primerIndice].emoji === this.cartas[segundoIndice].emoji) {

                    // Guarda el par encontrado y limpia las cartas visibles

                    this.paresEncontrados.push(primerIndice, segundoIndice)
                    this.cartasVisibles = []

                    // Verifica si el juego terminó

                    if (this.paresEncontrados.length === this.cartas.length) {
                        setTimeout(() => {
                            alert('Completaste el juego en ' + this.intentos + ' intentos')
                        }, 500)
                    }
                } else {

                    // Si no coinciden, espera 1 segundo y devuelve las cartas a su estado visible=false

                    await new Promise(resolve => setTimeout(resolve, 1000))
                    this.cartas[primerIndice].visible = false
                    this.cartas[segundoIndice].visible = false
                    this.cartasVisibles = []
                }
            }
        },

        // Restablece el juego

        reiniciarJuego() {
            this.cartasVisibles = []
            this.paresEncontrados = []
            this.intentos = 0
            this.iniciarJuego()
        }
    }
}
</script>