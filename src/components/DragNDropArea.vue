<script setup>
import Dropzone from 'dropzone'
import { onMounted, ref } from 'vue'
import { IfcAPI } from 'web-ifc'
import * as OBC from 'openbim-components'
import * as THREE from 'three'

const dropForm = ref()
const sceneArea = ref()
const clickForm = (elem) => dropForm.value.click()
let showDragNDrop = ref(true)

onMounted(() => {
  let dropzone = new Dropzone('#drop-form', {
    addedfile: async (file) => {
      showDragNDrop.value = false
      const components = new OBC.Components()
      components.scene = new OBC.SimpleScene(components)
      components.renderer = new OBC.SimpleRenderer(components, sceneArea.value)
      components.camera = new OBC.SimpleCamera(components)
      components.raycaster = new OBC.SimpleRaycaster(components)
      components.init()

      //window.components = components
      const fragmentIfcLoader = new OBC.FragmentIfcLoader(components)
      fragmentIfcLoader.settings.wasm = {
        path: 'https://unpkg.com/web-ifc@0.0.46/',
        absolute: true
      }

      //const fragments = new OBC.FragmentManager(components)
      const data = await file.arrayBuffer()
      const buffer = new Uint8Array(data)
      const model = await fragmentIfcLoader.load(buffer, 'example')
      components.scene.get().add(model)
      components.scene.setup()
      components.renderer.resize()
    },
    autoProcessQueue: false
  })
})
</script>

<template>
  <form v-if="showDragNDrop" id="drop-form" action="#" method="dialog" ref="dropForm">
    <p v-on:click="clickForm">
      Click here to select file or drag and drop file here to start upload.
    </p>
  </form>
  <div ref="sceneArea" id="sceneArea"></div>
</template>
