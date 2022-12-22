<script lang="ts">
  import { useTexture, useThrelte } from '@threlte/core';
  import * as THREE from 'three';

  // Falling Snow
  let particles: any; //snowflakes
  let positions: Array<number> = []; // snowflakes positions(x,y,z)
  let velocities: Array<number> = []; // snowflakes velocities(x,y,z)

  const numSnowflakes = 1500; // number of snowflakes

  const maxRange = 100; // snowflakes placed from -500 to 500 x & y axes
  const minRange = maxRange / 2;
  const minHeigth = 10; // snowflakes placed from 150 to 500 on y axis

  // BufferGeometry stores data as arry with individual attributes (position, color, size, faces, etc)
  const snowflakeGeometry = new THREE.BufferGeometry();

  const addSnowFlakes = () => {
    for (let i = 0; i < numSnowflakes; i++) {
      positions.push(
        Math.floor(Math.random() * maxRange - minRange), // x -500 to 500
        Math.floor(Math.random() * minRange + minHeigth), // y 250 to 750
        Math.floor(Math.random() * maxRange - minRange) // z -500 to 500
      );

      velocities.push(
        Math.floor(Math.random() * 6 - 3) * 0.01, // x -0.3 to 0.3
        Math.floor(Math.random() * 5 + 0.12) * 0.018, // y 0.02 to 0.92
        Math.floor(Math.random() * 6 - 3) * 0.01 // z -0.3 to 0.3
      );
    }

    snowflakeGeometry.setAttribute(
      'position',
      new THREE.Float32BufferAttribute(positions, 3)
    );
    snowflakeGeometry.setAttribute(
      'velocity',
      new THREE.Float32BufferAttribute(velocities, 3)
    );
  };

  // Snowflake material
  const flakeTexture = useTexture({
    map: 'snowflake.png',
  });

  const snowflakeMaterial = new THREE.PointsMaterial({
    size: 0.6,
    map: flakeTexture.map,
    blending: THREE.AdditiveBlending, // add RGB values when combining 2 colors
    depthTest: false, // determines if one object is in front of another
    transparent: true, // enable opacity changes to work
    opacity: 0.7,
  });

  const { scene } = useThrelte();
  particles = new THREE.Points(snowflakeGeometry, snowflakeMaterial);
  scene.add(particles);

  // update particles
  const updateParticles = () => {
    for (let i = 0; i < numSnowflakes * 3; i += 3) {
      particles.geometry.attributes.position.array[i] -=
        particles.geometry.attributes.velocity.array[i];

      particles.geometry.attributes.position.array[i + 1] -=
        particles.geometry.attributes.velocity.array[i + 1];

      particles.geometry.attributes.position.array[i + 2] -=
        particles.geometry.attributes.velocity.array[i + 2];

      if (particles.geometry.attributes.position.array[i + 1] < 0) {
        particles.geometry.attributes.position.array[i] = Math.floor(
          Math.random() * maxRange - minRange
        );
        particles.geometry.attributes.position.array[i + 1] =
          Math.floor(Math.random() * minRange - minHeigth);
        particles.geometry.attributes.position.array[i + 2] =
          Math.floor(Math.random() * maxRange - minRange);
      }
    }
    particles.geometry.attributes.position.needsUpdate = true;
  };

  const animate = () => {
    updateParticles();
    requestAnimationFrame(animate);
  };

  addSnowFlakes();
  animate();
</script>
