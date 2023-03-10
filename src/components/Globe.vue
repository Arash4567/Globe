<script setup>
import * as THREE from 'three';
import vertexShader from '../shaders/vertex.glsl'
import fragmentShader from '../shaders/fragment.glsl'
import atomosphereVertexShader from '../shaders/atomosphereVertex.glsl'
import atomosphereFragmentShader from '../shaders/atomosphereFragment.glsl'

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(70, window.innerWidth / window.innerHeight, 0.01, 1000);

const renderer = new THREE.WebGLRenderer({
  antialias: true
});
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio)
renderer.setAnimationLoop(animate);
document.body.appendChild(renderer.domElement);

const sphere = new THREE.Mesh(
  new THREE.SphereGeometry(5, 50, 50),
  new THREE.ShaderMaterial({
    vertexShader,
    fragmentShader,
    uniforms: {
      globeTexture: {
        value: new THREE.TextureLoader().load('/globe3.jpg')
      }
    }
  })
)

const atomosphere = new THREE.Mesh(
  new THREE.SphereGeometry(5, 50, 50),
  new THREE.ShaderMaterial({
    vertexShader: atomosphereVertexShader,
    fragmentShader: atomosphereFragmentShader,
    blending: THREE.AdditiveBlending,
    side: THREE.BackSide
  })
)

atomosphere.scale.set(1.1, 1.1, 1.1)

scene.add(atomosphere)

const group = new THREE.Group()
group.add(sphere)
scene.add(group)

camera.position.z = 15

window.addEventListener("mousemove", onMouseMove, false);

var lastMove = [window.innerWidth/2, window.innerHeight/2];

function onMouseMove(e) {
  e = e || window.event;

  //calculate difference between current and last mouse position
  const moveX = ( e.clientX - lastMove[0]);
  const moveY = ( e.clientY - lastMove[1]);
  //rotate the globe based on distance of mouse moves (x and y) 
  group.rotation.y += ( moveX * .005);
  group.rotation.x += ( moveY * .005);

  //store new position in lastMove
  lastMove[0] = e.clientX;
  lastMove[1] = e.clientY;
}

// animation
function animate() {
  requestAnimationFrame(animate)
  renderer.render(scene, camera);
  group.rotation.y += 0.00001
}

animate()
</script>

<template></template>

<style scoped></style>
