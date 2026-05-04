<script lang="ts">
  import { fade } from 'svelte/transition';
  import { onDestroy, onMount, tick } from 'svelte';
  import { browser } from '$app/environment';
  import { base } from '$app/paths';

  const waveFrames = ['/Wave frame 1.png', '/Wave frame 2.png'];

  const turtleFrames = ['/Turtle small 1.jpg', '/Turtle small 2.jpg'];

  /** Max downward travel (px) so the turtle stays inside the band above the wave. */
  const MAX_TURTLE_TRAVEL = 1;

  let waveIndex = $state(0);
  let turtleIndex = $state(0);
  let turtleOffset = $state(0);

  /** Plain `let` so `bind:this` assigns reliably (DOM refs should not use `$state`). */
  let sectionRoot: HTMLDivElement | undefined;
  let waveIntervalId: ReturnType<typeof setInterval> | null = null;
  let turtleIntervalId: ReturnType<typeof setInterval> | null = null;

  function normalizeStaticPath(raw: string) {
    const s = raw.trim();
    if (!s) return s;
    if (/^https?:\/\//i.test(s)) return s;
    const path = s.startsWith('/') ? s : '/' + s;
    return `${base}${path}`;
  }

  function updateParallax() {
    if (!browser) return;
    const el = sectionRoot;
    if (!el) return;

    const r = el.getBoundingClientRect();
    const vh = window.innerHeight;
    // Use a viewport-sized "journey" — not `vh + r.height` (wave is very tall, so that
    // made `p` almost flat and the turtle looked frozen while scrolling).
    const traveled = vh - r.top;
    const journey = Math.max(vh * 1.25, 400);
    const p = Math.max(0, Math.min(1, traveled / journey));
    turtleOffset = p * MAX_TURTLE_TRAVEL;
  }

  function onScrollOrResize() {
    updateParallax();
  }

  const scrollOpts = { passive: true, capture: true } as const;

  onMount(() => {
    waveIntervalId = setInterval(() => {
      waveIndex = (waveIndex + 1) % waveFrames.length;
    }, 1000);

    turtleIntervalId = setInterval(() => {
      turtleIndex = (turtleIndex + 1) % turtleFrames.length;
    }, 1000);

    void tick().then(() => updateParallax());

    window.addEventListener('scroll', onScrollOrResize, scrollOpts);
    document.addEventListener('scroll', onScrollOrResize, scrollOpts);
    window.addEventListener('resize', onScrollOrResize);
  });

  onDestroy(() => {
    if (waveIntervalId) clearInterval(waveIntervalId);
    if (turtleIntervalId) clearInterval(turtleIntervalId);
    if (browser) {
      window.removeEventListener('scroll', onScrollOrResize, scrollOpts);
      document.removeEventListener('scroll', onScrollOrResize, scrollOpts);
      window.removeEventListener('resize', onScrollOrResize);
    }
  });
</script>

<style>
  .wave-section {
    width: 100%;
  }

  .turtle-wrapper {
    position: sticky;
    width: 100%;
    height: 200px;
    /* overflow: hidden; */
    top: 0;
    z-index: 0;
  }

  .turtle-image {
    position: absolute;
    top: 0;
    left: 50%;
    width: auto;
    height: 100%;
    min-height: 80px;
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

<div class="wave-section" bind:this={sectionRoot}>
  <div class="turtle-wrapper">
    {#key turtleIndex}
      <img
        class="turtle-image"
        src={normalizeStaticPath(turtleFrames[turtleIndex])}
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
        src={normalizeStaticPath(waveFrames[waveIndex])}
        alt="Animated wave frame"
        loading="lazy"
        transition:fade={{ duration: 10 }}
      />
    {/key}
  </div>
</div>
