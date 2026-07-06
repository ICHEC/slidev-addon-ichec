<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

const props = defineProps({
  state: { type: String, default: '0' },
  debugCamera: { type: Boolean, default: false } // NEW PROP
})

const container = ref(null)
const cameraDebugText = ref('Drag to update camera...') // NEW STATE
let scene, camera, renderer, waveMesh, slit, slitGroup, animationId, controls
let currentAngle = 0
let targetAngle = 0

const getTargetAngle = (state) => {
  if (state === '0') return 0                   
  if (state === '1') return Math.PI / 2         
  if (state === '+') return Math.PI / 4         
  if (state === '-') return 3 * Math.PI / 4     
  return 0
}

targetAngle = getTargetAngle(props.state)
currentAngle = targetAngle

watch(() => props.state, (newVal) => {
  targetAngle = getTargetAngle(newVal)
})

class WaveCurve extends THREE.Curve {
  constructor(time, angle) {
    super()
    this.time = time
    this.angle = angle
  }
  getPoint(t, optionalTarget = new THREE.Vector3()) {
    const x = t * 20 
    const rawAmp = Math.sin(x * 1.5 - this.time) * 3
    const dampening = Math.min(1, x / 2)
    const amp = rawAmp * dampening
    const y = amp * Math.sin(this.angle)
    const z = amp * Math.cos(this.angle)
    return optionalTarget.set(x, y, z)
  }
}

onMounted(() => {
  scene = new THREE.Scene()
  
  camera = new THREE.PerspectiveCamera(45, 1, 0.1, 100)
  camera.up.set(0, 0, 1) 
  camera.position.set(25, 20, 15)
  
  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
  renderer.setSize(400, 400)
  renderer.setClearColor(0x000000, 0) 
  container.value.appendChild(renderer.domElement)

  controls = new OrbitControls(camera, renderer.domElement)
  controls.enableDamping = true
  controls.dampingFactor = 0.05
  controls.target.set(10, 0, 0) 

  // NEW: Update debug text when camera moves
  if (props.debugCamera) {
    controls.addEventListener('change', () => {
      const p = camera.position
      const t = controls.target
      cameraDebugText.value = `camera.position.set(${p.x.toFixed(1)}, ${p.y.toFixed(1)}, ${p.z.toFixed(1)})\ncontrols.target.set(${t.x.toFixed(1)}, ${t.y.toFixed(1)}, ${t.z.toFixed(1)})`
    })
  }

  const origin = new THREE.Vector3(0, 0, 0)
  const arrowX = new THREE.ArrowHelper(new THREE.Vector3(1, 0, 0), origin, 22, 0xff3333, 1, 0.5)
  const arrowY = new THREE.ArrowHelper(new THREE.Vector3(0, 1, 0), origin, 8, 0x33ff33, 1, 0.5)
  const arrowZ = new THREE.ArrowHelper(new THREE.Vector3(0, 0, 1), origin, 8, 0x3333ff, 1, 0.5)
  scene.add(arrowX, arrowY, arrowZ)

  const filterDistance = 10
  const planeGeo = new THREE.PlaneGeometry(10, 10)
  const planeMat = new THREE.MeshBasicMaterial({ color: 0x3b82f6, transparent: true, opacity: 0.8, side: THREE.DoubleSide })
  const polarizer = new THREE.Mesh(planeGeo, planeMat)
  polarizer.position.set(filterDistance, 0, 0)
  polarizer.rotation.y = Math.PI / 2
  scene.add(polarizer)

  const planeY = new THREE.ArrowHelper(new THREE.Vector3(0, 1, 0), new THREE.Vector3(filterDistance, 0, 0), 4.5, 0x33ff33, 0.6, 0.3)
  const planeZ = new THREE.ArrowHelper(new THREE.Vector3(0, 0, 1), new THREE.Vector3(filterDistance, 0, 0), 4.5, 0x3333ff, 0.6, 0.3)
  scene.add(planeY, planeZ)

  slitGroup = new THREE.Group()
  slitGroup.position.set(filterDistance + 0.05, 0, 0) 
  slitGroup.rotation.y = Math.PI / 2
  scene.add(slitGroup)

  const slitGeo = new THREE.PlaneGeometry(9, 0.3) 
  const slitMat = new THREE.MeshBasicMaterial({ color: 0xffffff, transparent: true, opacity: 0.9, side: THREE.DoubleSide })
  slit = new THREE.Mesh(slitGeo, slitMat)
  slitGroup.add(slit)

  const waveMaterial = new THREE.MeshBasicMaterial({ color: 0x10b981 })
  waveMesh = new THREE.Mesh(new THREE.BufferGeometry(), waveMaterial)
  scene.add(waveMesh)

  let time = 0
  const animate = () => {
    time += 0.06 
    currentAngle += (targetAngle - currentAngle) * 0.05
    slit.rotation.z = -currentAngle

    const waveCurve = new WaveCurve(time, currentAngle)
    const newWaveGeo = new THREE.TubeGeometry(waveCurve, 150, 0.12, 8, false)
    
    waveMesh.geometry.dispose() 
    waveMesh.geometry = newWaveGeo

    controls.update() 
    renderer.render(scene, camera)
    animationId = requestAnimationFrame(animate)
  }

  animate()
})

onUnmounted(() => {
  cancelAnimationFrame(animationId)
  if (waveMesh) waveMesh.geometry.dispose()
  renderer.dispose()
})
</script>

<template>
  <div class="relative w-[400px] mx-auto text-current font-mono">
    <div ref="container" class="w-[400px] h-[400px] cursor-move"></div>
    
    <div class="absolute top-2 left-2 text-xl font-bold opacity-80">
      |{{ state }}⟩
    </div>

    <div v-if="debugCamera" class="absolute bottom-2 left-2 right-2 bg-black/80 text-green-400 text-xs p-2 rounded whitespace-pre">
      {{ cameraDebugText }}
    </div>
  </div>
</template>