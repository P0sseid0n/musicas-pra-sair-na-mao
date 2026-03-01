<script setup lang="ts">
import LogoHeader from './components/LogoHeader.vue'
import { onMounted, ref, shallowRef, useTemplateRef } from 'vue'
import { Canvas, FabricImage, Point } from 'fabric'

const canvasRef = useTemplateRef('canvasRef')
const fileInputRef = useTemplateRef('fileInputRef')
const hasImage = ref(false)
const canvas = shallowRef<Canvas>()

function handleImageUpload(event: Event) {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]

  hasImage.value = false

  if (!file) return

  const reader = new FileReader()

  reader.onload = async (event) => {
    const imageResult = event.target?.result
    if (typeof imageResult !== 'string' || !canvas.value) return

    canvas.value.clear()

    const [userImg, overlayImg] = await Promise.all([
      FabricImage.fromURL(imageResult),
      FabricImage.fromURL('/overlay.png')
    ])

    const CANVAS_WIDTH = 640
    const CANVAS_HEIGHT = userImg.height * (CANVAS_WIDTH / userImg.width)

    canvas.value.setDimensions({ width: CANVAS_WIDTH, height: CANVAS_HEIGHT })

    userImg.set({
      selectable: false,
      evented: false
    })

    overlayImg.set({
      selectable: false,
      evented: false
    })

    userImg.scaleToWidth(canvas.value.getWidth())
    overlayImg.scaleToWidth(canvas.value.getWidth())

    userImg.positionByLeftTop(new Point(0, 0))
    overlayImg.positionByLeftTop(new Point(0, 0))

    canvas.value.add(userImg, overlayImg)

    hasImage.value = true
  }

  reader.readAsDataURL(file)
}

function downloadImage() {
  const dataURL = canvasRef.value!.toDataURL()

  const link = document.createElement('a')
  link.href = dataURL
  link.download = 'image.png'

  link.click()
}

onMounted(() => {
  canvas.value = new Canvas(canvasRef.value!, {
    selection: false,
    renderOnAddRemove: true
  })
})
</script>

<template>
  <div class="made">
    Feito por
    <a href="https://github.com/P0sseid0n" target="_blank" rel="noopener noreferrer">P0sseid0n</a>
  </div>
  <LogoHeader />

  <main>
    <div id="canvas_container" :class="{ 'has-image': hasImage }">
      <canvas ref="canvasRef"></canvas>
    </div>

    <div id="buttons_container">
      <button v-if="hasImage" class="download" @click="downloadImage">Baixar imagem</button>

      <input ref="fileInputRef" type="file" accept="image/*" @change="handleImageUpload" hidden />
      <button class="choose" @click="fileInputRef?.click">Escolher imagem</button>
    </div>
  </main>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;

  font-family: 'Rubik', sans-serif;
  font-optical-sizing: auto;
  font-style: normal;
}

#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100vh;

  background-color: #fe0000;
  color: white;
}

.made {
  width: 100%;
  background-color: #c90212;
  text-align: center;
  padding: 8px 0;
}

.made a {
  color: white;
  text-decoration: none;
  font-weight: bold;
}

main {
  display: flex;
  flex-direction: column;
  align-items: center;

  width: 100%;
  max-width: 640px;
}

#canvas_container {
  padding: 16px;
  border-radius: 8px;
}

#canvas_container.has-image {
  background-color: white;
}

#buttons_container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 32px;
  width: 100%;
}

button.download {
  padding: 12px 16px;
  background-color: white;
  color: #fe0000;
  border: none;
  border-radius: 4px;
  font-weight: 500;
  font-size: 1.25rem;
  cursor: pointer;
  width: 100%;
  margin-top: 48px;
}

button.choose {
  padding: 8px 48px;
  background-color: white;
  color: #fe0000;
  border: none;
  border-radius: 2px;
  font-weight: 500;
  font-size: 1rem;
  cursor: pointer;
  width: fit-content;
}
</style>
