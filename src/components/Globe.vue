<script setup>
import * as THREE from "three";
import ThreeGlobe from "three-globe";
import vertexShader from '../shaders/vertex.glsl'
import fragmentShader from '../shaders/fragment.glsl'
import countries from "../assets/data/globe-data-min.json";
import { TrackballControls } from "three/examples/jsm/controls/TrackballControls";

const Globe = new ThreeGlobe(
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
  .showAtmosphere(true)
  .hexPolygonsData(countries.features)
  .hexPolygonResolution(3)
  .showGraticules(false)
  .hexPolygonMargin(0.1)
  .hexPolygonColor((e) => {
    if (["USA", "UZB", "RUS"].includes(e.properties.ISO_A3)) {
      return "#f00";
    } else return "#d4d4d4";
  });

const globeMaterial = Globe.globeMaterial();
globeMaterial.opacity = 1;
globeMaterial.color = new THREE.Color(0x00f);

// Setup renderer
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
document.body.appendChild(renderer.domElement);

// Setup scene
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x000);
scene.add(Globe);
scene.add(new THREE.AmbientLight(0xbcbcbc, 2));
scene.add(new THREE.AmbientLight(0xbcbcbc, 2));

// Setup camera
const camera = new THREE.PerspectiveCamera();
camera.aspect = window.innerWidth / window.innerHeight;
camera.updateProjectionMatrix();
camera.position.z = 300;

// Add camera controls
const tbControls = new TrackballControls(camera, renderer.domElement);
tbControls.minDistance = 300;
tbControls.maxDistance = 300;
tbControls.rotateSpeed = 3;

const clock = new THREE.Clock();

// Kick-off renderer
function animate() {
  // IIFE
  const elapsedTime = clock.getElapsedTime();
  Globe.rotation.y = elapsedTime / 10;
  // Frame cycle
  tbControls.update();
  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}

animate();
</script>

<template>
  <h1>Hello World!</h1>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

h1 {
  position: absolute;
}
</style>
