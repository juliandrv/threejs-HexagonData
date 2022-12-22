<script lang="ts">
  import { tweened } from 'svelte/motion';
  import { fade } from 'svelte/transition';

  import { browser } from '$app/environment';

  import {
    Canvas,
    PerspectiveCamera,
    OrbitControls,
    AmbientLight,
    DirectionalLight,
    Mesh,
    useTexture,
  } from '@threlte/core';
  import * as Extra from '@threlte/extras';
  import * as THREE from 'three';

  import FallingSnow from '$lib/FallingSnow.svelte';
  //import TextMessage from '$lib/TextMessage.svelte';

  let camera = {
    position: { x: 10, y: 10, z: 26 },
  };

  let santa = {
    position: { x: 40, y: 20, z: -1 },
    scale: 1,
    rotation: { y: 0 },
  };

  let world = {
    position: { x: 0, y: 0.001, z: 0 },
  };

  // Floor
  const textures = useTexture({
    normalMap: 'materials/normal.jpg',
    map: 'materials/color.jpg',
    displacementMap: 'materials/height.png',
    aoMap: 'materials/occlusion.jpg',
    roughnessMap: 'materials/roughness.jpg',
  });

  textures.normalMap.repeat.set(8, 8);
  textures.map.repeat.set(8, 8);
  textures.displacementMap.repeat.set(8, 8);
  textures.aoMap.repeat.set(8, 8);
  textures.roughnessMap.repeat.set(8, 8);

  textures.normalMap.wrapS = THREE.RepeatWrapping;
  textures.map.wrapS = THREE.RepeatWrapping;
  textures.displacementMap.wrapS = THREE.RepeatWrapping;
  textures.aoMap.wrapS = THREE.RepeatWrapping;
  textures.roughnessMap.wrapS = THREE.RepeatWrapping;

  textures.normalMap.wrapT = THREE.RepeatWrapping;
  textures.map.wrapT = THREE.RepeatWrapping;
  textures.displacementMap.wrapT = THREE.RepeatWrapping;
  textures.aoMap.wrapT = THREE.RepeatWrapping;
  textures.roughnessMap.wrapT = THREE.RepeatWrapping;

  // Update Scene
  const clock = new THREE.Clock();

  const tick = () => {
    const elapsedTime = clock.getElapsedTime();

    const santaAngle = elapsedTime * -0.5;
    santa.position.x = Math.cos(santaAngle) * 10;
    santa.position.y = Math.sin(elapsedTime * 2) + 6;
    santa.position.z = Math.sin(santaAngle) * 10;
    //santa.rotation.y = -santaAngle * 0.2;

    requestAnimationFrame(tick);
  };

  if (browser) {
    tick();
  }

  // Loading component
  const { progress } = Extra.useProgress();
  const tweenedProgress = tweened($progress, {
    duration: 800,
  });

  $: tweenedProgress.set($progress);
</script>

<svelte:head>
  <title>3D Christmas</title>
</svelte:head>

{#if $tweenedProgress < 1}
  <div
    transition:fade|local={{
      duration: 200,
    }}
    class="wrapper"
  >
    <p class="loading">Cargando experiencia</p>
    <div class="bar-wrapper">
      <div class="bar" style="width: {$tweenedProgress * 100}%" />
    </div>
  </div>
{/if}

<Canvas rendererParameters={{ antialias: true }}>
  <PerspectiveCamera {...camera}>
    <OrbitControls
      enableDamping={true}
      minDistance={10}
      maxDistance={50}
      minAzimuthAngle={-Math.PI * 0.5}
      maxAzimuthAngle={Math.PI * 0.5}
      maxPolarAngle={Math.PI * 0.499}
      target={{ y: 6 }}
    />
  </PerspectiveCamera>

  <AmbientLight color="white" intensity={0.4} />
  <DirectionalLight
    color="white"
    intensity={0.4}
    shadow
    position={{ x: -10, y: 20, z: 20 }}
  />

  <Extra.GLTF
    url="models/santa2.glb"
    {...santa}
    castShadow
    receiveShadow
  />
  <Extra.GLTF url="models/world.glb" {...world} receiveShadow />

  <Mesh
    geometry={new THREE.PlaneGeometry(30, 30)}
    material={new THREE.MeshStandardMaterial({
      ...textures,
      color: '#c6cbd8',
      displacementScale: 0,
    })}
    rotation={{ x: -Math.PI * 0.5 }}
    receiveShadow
  />

  <Extra.Text
    text={'Felices\nFiestas'}
    scale={96}
    lineHeight={0.8}
    position={{ x: 0, y: 15, z: -15 }}
    anchorX={'center'}
  />
  <FallingSnow />
  <!-- <TextMessage /> -->
</Canvas>

<img class="logo" src="logo.png" alt="logo vass" width="10%" />
<a
  href="https://juliandrv.com"
  target="_blank"
  rel="noreferrer"
  class="copyrights">by juliandrv</a
>

<style>
  .wrapper {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background: radial-gradient(rgb(40, 48, 63), rgb(9, 38, 85));
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
    align-items: center;
    justify-content: center;
    color: white;
  }

  .loading {
    font-size: 0.875rem;
    line-height: 1.25rem;
  }

  .bar-wrapper {
    width: 33.333333%;
    height: 10px;
    border: 1px solid white;
    position: relative;
  }

  .bar {
    height: 100%;
    background-color: white;
  }
  .logo {
    position: absolute;
    left: 0;
    top: 0;
    padding: 10px;
  }
  .copyrights {
    position: absolute;
    bottom: 0;
    right: 0;
    margin: 0;
    color: white;
    font-family: Arial, Helvetica, sans-serif;
    padding: 10px;
  }
</style>
