<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-900 via-indigo-900 to-blue-900 flex flex-col items-center justify-center p-4">
    <h1 class="text-3xl font-bold text-yellow-400 mb-6 drop-shadow-lg">
      🎰 Ticket Gagnant 🎰
    </h1>
    
    <div class="relative rounded-xl overflow-hidden shadow-2xl border-4 border-yellow-500">
      <!-- Image cachée derrière -->
      <div 
        class="w-80 h-[40rem] flex flex-col items-center"
        
      >
      <img src="@/assets/nhow-marseille.jpg" />
      <div class="p-5 h-1/3 justify-self-center">

        <img src="@/assets/nhow-spa.jpg" class="h-full" />
      </div>
      <img src="@/assets/nhow-chambre.jpg" class="h-full" />
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