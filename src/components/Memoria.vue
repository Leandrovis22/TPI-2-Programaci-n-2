<template>
    <div class="max-w-2xl mx-auto">
        <div class="text-center mb-4">
            <h2 class="text-2xl font-bold">Intentos: {{ intentos }}</h2>
            <p>Pares encontrados: {{ paresEncontrados.length / 2 }}</p>
        </div>

        <div class="grid grid-cols-6 gap-4">
            <div v-for="(carta, indice) in cartas" :key="indice" @click="voltearCarta(indice)" class="aspect-square">
                <div class="w-full h-full transition-transform duration-500" :class="[ 
                    'rounded-lg border-2', 
                    carta.volteada || paresEncontrados.includes(indice) 
                        ? 'bg-white' 
                        : 'bg-blue-500' 
                ]">
                    <span v-if="carta.volteada || paresEncontrados.includes(indice)"
                        class="flex items-center justify-center h-full text-5xl">
                        {{ carta.emoticono }}
                    </span>
                </div>
            </div>
        </div>

        <button @click="reiniciarJuego" class="w-full mt-4 py-2 bg-green-500 text-white rounded hover:bg-green-600">
            Reiniciar
        </button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            cartas: [],
            cartasVolteadas: [],
            paresEncontrados: [],
            intentos: 0,
            emoticonos: ['🐶', '🐱', '🐲', '🐹', '🐰', '🦊', '🐻', '🐼','🐎']
        }
    },
    created() {
        this.inicializarJuego()
    },
    methods: {
        inicializarJuego() {
            this.cartas = [...this.emoticonos, ...this.emoticonos]
                .map((emoticono) => ({
                    emoticono,
                    volteada: false
                }))
                .sort(() => Math.random() - 0.5)
        },
        async voltearCarta(indice) {
            if (
                this.cartasVolteadas.length === 2 ||
                this.paresEncontrados.includes(indice) ||
                this.cartasVolteadas.includes(indice)
            ) {
                return
            }

            this.cartas[indice].volteada = true
            this.cartasVolteadas.push(indice)

            if (this.cartasVolteadas.length === 2) {
                this.intentos++
                const [primerIndice, segundoIndice] = this.cartasVolteadas

                if (this.cartas[primerIndice].emoticono === this.cartas[segundoIndice].emoticono) {
                    this.paresEncontrados.push(primerIndice, segundoIndice)
                    this.cartasVolteadas = []

                    if (this.paresEncontrados.length === this.cartas.length) {
                        setTimeout(() => {
                            alert('Completaste el juego en ' + this.intentos + ' intentos')
                        }, 500)
                    }
                } else {
                    await new Promise(resolve => setTimeout(resolve, 1000))
                    this.cartas[primerIndice].volteada = false
                    this.cartas[segundoIndice].volteada = false
                    this.cartasVolteadas = []
                }
            }
        },
        reiniciarJuego() {
            this.cartasVolteadas = []
            this.paresEncontrados = []
            this.intentos = 0
            this.inicializarJuego()
        }
    }
}
</script>
