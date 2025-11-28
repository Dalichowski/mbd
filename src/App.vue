<!-- <template>
  <div
    class="bg-black text-white min-h-screen flex flex-col text-center justify-center p-5"
  >
  <div v-if="!started" >

    <div class="mb-5">
      <div class="text-lg">Bonjour Marie,</div>

      <div>Afin de découvrir ton cadeau d'anniversaire</div>
      <div>Voici quelques étapes préliminaires ...</div>
    </div>

    <NButton type="primary" @click="Object.assign(this, {started: true, step : 1, firstClue: true })">
      Démarrer
    </NButton>
  </div>

  <div v-if="step === 1 && firstClue">
    <div>PREMIERE QUESTION</div>
    Voici le premier indice:
    <img src="@/assets/Léon_Blum.jpg" />

    <NButton type="secondary" @click="Object.assign(this, { firstClue: false, secondClue: true})">
      Un autre indice ?
    </NButton>
    <NButton type="primary" @click="step = 2" class="mt-5">
      Non, j'ai la réponse
    </NButton>
  </div>

  <div v-if="step === 1 && secondClue">
    <div>PREMIERE QUESTION</div>
    Un autre indice:
    <img src="@/assets/orlando_bloom.jpg" />

    <NButton type="primary" @click="Object.assign(this, { secondClue: false, step: 2})" class="mt-5">
      Suivant
    </NButton>
  </div>

  <div v-if="step === 2">
    Entre ta réponse:
    <NInput v-model:value="answerOne" />
    <NButton type="primary" class="mt-5" @click="checkAnswerOne">
      VALIDER
    </NButton>
  </div>

  <div v-if="step === 3" >

    <div class="mb-5">
      <div class="text-lg">Bien joué,</div>

      <div>Tu as trouvé le premier indice de ton cadeau</div>
      <div>Clique pour continuer ...</div>
    </div>

    <NButton type="primary" @click="Object.assign(this, {step : 4, thirdClue: true })">
      Continuer
    </NButton>
  </div>

  <div v-if="step === 4 && thirdClue">
    <div>SECONDE QUESTION</div>
    Voici le premier indice:
    <img src="@/assets/Laurent_Garnier.png" />

    <NButton type="secondary" @click="Object.assign(this, { thirdClue: false, fourthClue: true})">
      Un autre indice ?
    </NButton>
    <NButton type="primary" @click="step = 5" class="mt-5">
      Non, j'ai la réponse
    </NButton>
  </div>

  <div v-if="step === 4 && fourthClue">
    <div>SECONDE QUESTION</div>
    Un autre indice:
    <img src="@/assets/dr_house.jpg" />

    <NButton type="primary" @click="Object.assign(this, { fourthClue: false, step: 5})" class="mt-5">
      Suivant
    </NButton>
  </div>

  <div v-if="step === 5">
    Entre ta réponse:
    <NInput v-model:value="answerTwo" />
    <NButton type="primary" class="mt-5" @click="checkAnswerTwo">
      VALIDER
    </NButton>
  </div>

  <div v-if="step === 6">
    Voici ton cadeau:
    <a href="https://bloomhouse-hotel.com" class="bg-blue-500 underline">
      {{ answerOne }} {{ answerTwo }}
    </a> (clique pour découvrir)
  </div>

  </div>
</template>

<script>
import { notification } from "@/plugins/naiveUI.js";

export default {
  data: () => ({
    started: false,
    step: 0,
    firstClue: false,
    secondClue: false,
    thirdClue: false,
    fourthClue: false,
    answerOne: "",
    answerTwo: ""
  }),
  methods: {
    checkAnswerOne() {
      const regex = /^(bloom|blum)$/i;
      if (regex.test(this.answerOne)) {
        notification.success({
          title: "BRAVO !",
          content: "Tu as trouvé !",
          duration: 2500
        });
        Object.assign(this, { step: 3, firstClue: false, secondClue: false })
      } else {
        notification.success({
          title: "Eh non ...",
          content: "Dommage, essaye encore ...",
          duration: 2500
        });
        setTimeout(() => {
          Object.assign(this, { step: 1, firstClue: false, secondClue: true })
        }, 1500);
      }
    },
    checkAnswerTwo() {
      const regex = /^(house)$/i;
      if (regex.test(this.answerTwo)) {
        notification.success({
          title: "BRAVO !",
          content: "Tu as trouvé !",
          duration: 2500
        });
        Object.assign(this, { step: 6, thirdClue: false, fourthClue: false })
      } else {
        notification.success({
          title: "Eh non ...",
          content: "Dommage, essaye encore ...",
          duration: 2500
        });
        setTimeout(() => {
          Object.assign(this, { step: 4, thirdClue: false, fourthClue: true })
        }, 1500);
      }
    }
  }
};
</script> -->
<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-900 via-indigo-900 to-blue-900 flex flex-col items-center justify-center p-4">
    <h1 class="text-3xl font-bold text-yellow-400 mb-6 drop-shadow-lg">
      🎰 Ticket Gagnant 🎰
    </h1>
    
    <div class="relative rounded-xl overflow-hidden shadow-2xl border-4 border-yellow-500">
      <!-- Image cachée derrière -->
      <div 
        class="w-80 h-[30rem] grid grid-rows-3"
        
      >
        <img src="@/assets/nhow-chambre.jpg" />
        <img src="@/assets/nhow-marseille.jpg" />
        <img src="@/assets/nhow-spa.jpg" />
      </div>
        
      
      <!-- Canvas pour gratter -->
      <canvas
        ref="canvasRef"
        :width="1000"
        :height="1800"
        class="absolute top-0 left-0 cursor-pointer"
        @mousedown="handleStart"
        @mousemove="scratch"
        @mouseup="handleEnd"
        @mouseleave="handleEnd"
        @touchstart.prevent="handleStart"
        @touchmove="scratch"
        @touchend="handleEnd"
      />
    </div>
    
    <div class="mt-4 text-white/80 text-sm">
      Gratté : {{ scratchPercent }}%
    </div>
    
    
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  name: 'ScratchCard',
  setup() {
    const canvasRef = ref(null)
    const isScratching = ref(false)
    const scratchPercent = ref(0)
    const revealed = ref(false)

    const initCanvas = () => {
      const canvas = canvasRef.value
      const ctx = canvas.getContext('2d')
      
      // Couche grise à gratter
      ctx.fillStyle = '#a0a0a0'
      ctx.fillRect(0, 0, canvas.width, canvas.height)
      
      // Texture de ticket
      ctx.fillStyle = '#888'
      ctx.font = '14px Arial'
      for (let y = 20; y < canvas.height; y += 30) {
        for (let x = 10; x < canvas.width; x += 80) {
          ctx.fillText('GRATTEZ', x, y)
        }
      }
    }

    const calculateScratchPercent = (ctx, canvas) => {
      const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height)
      const pixels = imageData.data
      let transparent = 0
      for (let i = 3; i < pixels.length; i += 4) {
        if (pixels[i] === 0) transparent++
      }
      return (transparent / (pixels.length / 4)) * 100
    }

    const scratch = (e) => {
      if (!isScratching.value) return
      
      const canvas = canvasRef.value
      const ctx = canvas.getContext('2d')
      const rect = canvas.getBoundingClientRect()
      
      const clientX = e.clientX ?? e.touches?.[0]?.clientX
      const clientY = e.clientY ?? e.touches?.[0]?.clientY
      
      const x = clientX - rect.left
      const y = clientY - rect.top
      
      ctx.globalCompositeOperation = 'destination-out'
      ctx.beginPath()
      ctx.arc(x, y, 25, 0, Math.PI * 2)
      ctx.fill()
      
      const percent = calculateScratchPercent(ctx, canvas)
      scratchPercent.value = Math.round(percent)
      
      if (percent > 60 && !revealed.value) {
        revealed.value = true
      }
    }

    const handleStart = (e) => {
      isScratching.value = true
      scratch(e)
    }

    const handleEnd = () => {
      isScratching.value = false
    }

    const reset = () => {
      revealed.value = false
      scratchPercent.value = 0
      initCanvas()
    }

    onMounted(initCanvas)

    return {
      canvasRef,
      scratchPercent,
      revealed,
      handleStart,
      handleEnd,
      scratch,
      reset
    }
  }
}
</script>