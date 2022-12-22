<script lang="ts">
  import { useTexture, useThrelte } from '@threlte/core';
  import * as THREE from 'three';
  import { FontLoader } from 'three/examples/jsm/loaders/FontLoader';
  import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry';

  const textureLoader = new THREE.TextureLoader();
  const matcapTexture = textureLoader.load(
    '../textures/matcaps/1.jpg'
  );

  const fontLoader = new FontLoader();
  fontLoader.load(
    '../fonts/droid_sans_mono_regular.typeface.json',
    (font) => {
      let text;
      const textGeometry = new TextGeometry('Felices\nFiestas', {
        font: font,
        size: 3,
        height: 0.4,
        curveSegments: 20,
        bevelEnabled: true,
        bevelThickness: 0.03,
        bevelSize: 0.02,
        bevelOffset: 0.03,
        bevelSegments: 10,
      });
      textGeometry.center();

      const material = new THREE.MeshMatcapMaterial();
      material.matcap = matcapTexture;

      text = new THREE.Mesh(textGeometry, material);
      const { scene } = useThrelte();
      scene.add(text);
    }
  );
</script>
