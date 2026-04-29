<script lang="ts">
  import { fade } from 'svelte/transition';
  import { onDestroy, onMount } from 'svelte';

  const waveFrames = [
    '/Wave frame 1.png',
    '/Wave frame 2.png'
  ];

  const turtleFrames = [
    '/Turtle frame 1.jpg',
    '/Turtle frame 2.jpg'
  ];

  let waveIndex = 0;
  let turtleIndex = 0;
  let turtleOffset = 0;
  let waveIntervalId: ReturnType<typeof setInterval> | null = null;
  let turtleIntervalId: ReturnType<typeof setInterval> | null = null;
  let scrollHandler: (() => void) | null = null;

  onMount(() => {
    waveIntervalId = setInterval(() => {
      waveIndex = (waveIndex + 1) % waveFrames.length;
    }, 1000);

    turtleIntervalId = setInterval(() => {
      turtleIndex = (turtleIndex + 1) % turtleFrames.length;
    }, 1000);

    scrollHandler = () => {
      turtleOffset = window.scrollY * 0.5; // Parallax effect
    };
    window.addEventListener('scroll', scrollHandler);
  });

  onDestroy(() => {
    if (waveIntervalId) clearInterval(waveIntervalId);
    if (turtleIntervalId) clearInterval(turtleIntervalId);
    if (scrollHandler) window.removeEventListener('scroll', scrollHandler);
  });
</script>

<style>
  .turtle-wrapper {
    position: relative;
    width: 100%;
    height: 200px; /* Adjust height as needed */
    overflow: hidden;
    z-index: 0;
  }

  .turtle-image {
    position: absolute;
    top: 0;
    left: 50%;
    width: auto;
    height: 100%;
    object-fit: contain;
    display: block;
  }

  .wave-wrapper {
    position: relative;
    width: 100%;
    aspect-ratio: 2775 / 3848;
    overflow: hidden;
    z-index: 1;
  }

  .wave-image {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    object-fit: contain;
    display: block;
  }
</style>

<div class="turtle-wrapper">
  {#key turtleIndex}
    <img
      class="turtle-image"
      src={turtleFrames[turtleIndex]}
      alt="Animated turtle frame"
      loading="lazy"
      style="transform: translateX(-50%) translateY({turtleOffset}px);"
      transition:fade={{ duration: 300 }}
    />
  {/key}
</div>

<div class="wave-wrapper">
  {#key waveIndex}
    <img
      class="wave-image"
      src={waveFrames[waveIndex]}
      alt="Animated wave frame"
      loading="lazy"
      transition:fade={{ duration: 300 }}
    />
  {/key}
</div>