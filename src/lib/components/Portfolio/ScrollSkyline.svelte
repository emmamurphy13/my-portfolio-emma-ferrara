<script>
  import { onMount, onDestroy } from 'svelte';
  let scrollProgress = 0;

  // All buildings — heights grow from left (short) to right (tall) as user scrolls
  // Left buildings are static and short. Right buildings grow with scroll.
  const allBuildings = [
    // Static left — short
    { x: 2,  w: 6,  maxH: 18, delay: 0, static: true },
    { x: 7,  w: 5,  maxH: 22, delay: 0, static: true },
    { x: 11, w: 7,  maxH: 16, delay: 0, static: true },
    { x: 17, w: 5,  maxH: 20, delay: 0, static: true },
    { x: 21, w: 8,  maxH: 25, delay: 0, static: true },
    { x: 28, w: 6,  maxH: 18, delay: 0, static: true },
    { x: 33, w: 5,  maxH: 14, delay: 0, static: true },
    { x: 37, w: 7,  maxH: 20, delay: 0, static: true },
    { x: 43, w: 6,  maxH: 17, delay: 0, static: true },

    // Right — grow with scroll, getting progressively taller
    { x: 50, w: 7,  maxH: 35, delay: 0.00 },
    { x: 57, w: 6,  maxH: 44, delay: 0.10 },
    { x: 62, w: 8,  maxH: 55, delay: 0.20 },
    { x: 69, w: 6,  maxH: 48, delay: 0.28 },
    { x: 74, w: 9,  maxH: 65, delay: 0.36 },
    { x: 82, w: 7,  maxH: 52, delay: 0.44 },
    { x: 88, w: 10, maxH: 72, delay: 0.52 },
  ];

  const rightBuildings = allBuildings.filter(b => !b.static);

  function rng(seed) {
    let x = Math.sin(seed + 1) * 10000;
    return x - Math.floor(x);
  }

  function getBuildingH(b, progress) {
    if (b.static) return b.maxH;
    const p = Math.min(1, Math.max(0, (progress - b.delay) / (1 - b.delay + 0.001)));
    return b.maxH * (1 - Math.pow(1 - p, 3));
  }

  function getWindows(bx, bw, bh, idx) {
    const wins = [];
    const cols = bw >= 8 ? 3 : bw >= 6 ? 2 : 1;
    const ww = 0.9, wh = 1.2, gapX = (bw - cols * ww) / (cols + 1), gapY = 1.8;
    const rows = Math.max(1, Math.floor((bh - 3) / (wh + gapY)));
    for (let r = 0; r < rows; r++) {
      for (let c = 0; c < cols; c++) {
        const seed = idx * 200 + r * 10 + c;
        const lit = rng(seed) > 0.42;
        const amber = rng(seed + 0.3) > 0.45;
        wins.push({
          x: bx + gapX * (c + 1) + ww * c,
          fromBottom: 2.5 + r * (wh + gapY),
          w: ww, h: wh,
          color: lit ? (amber ? '#c9a227' : '#6bb8d4') : null,
        });
      }
    }
    return wins;
  }

  // Stars — seeded, upper sky only
  const stars = Array.from({ length: 60 }, (_, i) => ({
    x: (i * 137.508) % 100,
    y: rng(i * 7.3) * 32 + 1,
    size: rng(i * 13.1) * 3 + 1,
    opacity: rng(i * 17.7) * 0.5 + 0.25,
    twinkle: rng(i * 19.3) * 3 + 2,
  }));

  // Arrow: one point per building, at rooftop height, spanning full width
  // Left buildings are short, right buildings rise — arrow climbs left→right
  function computeArrow(progress) {
    // Sample a point above each building's rooftop
    const pts = allBuildings.map(b => {
      const h = getBuildingH(b, progress);
      return { x: b.x + b.w / 2, y: 100 - h };
    });

    // Only draw up to the last meaningfully-grown right building
    const lastGrownIdx = (() => {
      let last = allBuildings.findIndex(b => !b.static) - 1; // start just before right buildings
      for (let i = 0; i < allBuildings.length; i++) {
        const b = allBuildings[i];
        if (!b.static && getBuildingH(b, progress) > 2) last = i;
      }
      return last;
    })();

    const visiblePts = pts.slice(0, lastGrownIdx + 1);
    if (visiblePts.length < 2) return { d: '', tip: null, angle: 0 };

    // Start arrow from ground-left
    const groundStart = { x: pts[0].x, y: 99 };
    const allPts = [groundStart, ...visiblePts];

    const d = allPts.map((p, i) => `${i === 0 ? 'M' : 'L'}${p.x.toFixed(2)},${p.y.toFixed(2)}`).join(' ');
    const tip = allPts[allPts.length - 1];
    const prev = allPts[allPts.length - 2];
    const angle = Math.atan2(tip.y - prev.y, tip.x - prev.x) * 180 / Math.PI;

    return { d, tip, angle };
  }

  function handleScroll() {
    if (typeof window === 'undefined' || typeof document === 'undefined') return;
    const max = document.body.scrollHeight - window.innerHeight;
    scrollProgress = max > 0 ? Math.min(1, window.scrollY / max) : 0;
  }

  onMount(() => {
    if (typeof window === 'undefined') return;
    window.addEventListener('scroll', handleScroll, { passive: true });
    handleScroll();
  });
  onDestroy(() => { if (typeof window !== 'undefined') window.removeEventListener('scroll', handleScroll); });
</script>

<div class="scene">
  <svg
    class="skyline-svg"
    viewBox="0 0 100 100"
    preserveAspectRatio="xMidYMax slice"
    xmlns="http://www.w3.org/2000/svg"
  >
    <defs>
      <linearGradient id="sky" x1="0" y1="0" x2="0" y2="1">
        <stop offset="0%"   stop-color="#03080f" />
        <stop offset="55%"  stop-color="#0a1628" />
        <stop offset="100%" stop-color="#0f1f38" />
      </linearGradient>
      <!-- Arrow gradient: dark navy at base → bright red at tip, like the reference image -->
      <linearGradient id="arrowGrad" x1="0%" y1="100%" x2="100%" y2="0%">
        <stop offset="0%"   stop-color="#1a2a5e" />
        <stop offset="40%"  stop-color="#c0392b" />
        <stop offset="100%" stop-color="#ff4500" />
      </linearGradient>
      <linearGradient id="arrowGlow" x1="0%" y1="100%" x2="100%" y2="0%">
        <stop offset="0%"   stop-color="#1a2a5e" stop-opacity="0" />
        <stop offset="60%"  stop-color="#e74c3c" stop-opacity="0.4" />
        <stop offset="100%" stop-color="#ff6b35" stop-opacity="0.6" />
      </linearGradient>
    </defs>

    <!-- Sky -->
    <rect width="100" height="100" fill="url(#sky)" />

    <!-- Stars — upper third of sky -->
    {#each stars as s}
      <polygon
        points={Array.from({ length: 8 }, (_, j) => {
          const a = (Math.PI / 4) * j - Math.PI / 2;
          const r = j % 2 === 0 ? s.size / 2 : s.size / 5;
          return `${(s.x + Math.cos(a) * r).toFixed(2)},${(s.y + Math.sin(a) * r).toFixed(2)}`;
        }).join(' ')}
        fill="#b0c4de"
        opacity={s.opacity}
      >
        <animate
          attributeName="opacity"
          values="{s.opacity};{(s.opacity * 0.2).toFixed(2)};{s.opacity}"
          dur="{s.twinkle.toFixed(1)}s"
          repeatCount="indefinite"
        />
      </polygon>
    {/each}

    <!-- Moon -->
    <circle cx="88" cy="8" r="5"   fill="#ede8c8" opacity="0.1"  />
    <circle cx="88" cy="8" r="4"   fill="#ede8c8" opacity="0.92" />
    <circle cx="86.4" cy="7" r="3.6" fill="#0a1628" opacity="0.96" />

    <!-- Buildings -->
    {#each allBuildings as b, i}
      {@const h = getBuildingH(b, scrollProgress)}
      {#if h > 0.5}
        <!-- Building body — dark navy, matching reference image silhouette style -->
        <rect
          x={b.x} y={100 - h}
          width={b.w} height={h}
          fill="#141c2e"
        />
        <!-- Windows -->
        {#each getWindows(b.x, b.w, h, i) as w}
          {#if w.color && (100 - w.fromBottom - w.h) >= (100 - h + 2)}
            <rect
              x={w.x} y={100 - w.fromBottom - w.h}
              width={w.w} height={w.h}
              fill={w.color} rx="0.1"
            />
          {/if}
        {/each}
      {/if}
    {/each}

    <!-- Ground line -->
    <rect x="0" y="99" width="100" height="1" fill="#020609" />

    <!-- ARROW — rises over rooftops from left (low) to right (high) -->
    {#each [computeArrow(scrollProgress)] as arrow}
      {#if arrow.d}
        <!-- Wide glow behind arrow -->
        <path
          d={arrow.d} fill="none"
          stroke="url(#arrowGlow)"
          stroke-width="5"
          stroke-linecap="round" stroke-linejoin="round"
        />
        <!-- Main arrow line with gradient -->
        <path
          d={arrow.d} fill="none"
          stroke="url(#arrowGrad)"
          stroke-width="2.2"
          stroke-linecap="round" stroke-linejoin="round"
        />
        <!-- Arrowhead — big, bold, pointing in direction of travel -->
        {#if arrow.tip && scrollProgress > 0.01}
          <polygon
            points="0,-7 5.5,5 -5.5,5"
            fill="#ff4500"
            transform="translate({arrow.tip.x},{arrow.tip.y}) rotate({arrow.angle + 90})"
          />
          <!-- Arrowhead shine -->
          <polygon
            points="0,-7 5.5,5 -5.5,5"
            fill="none"
            stroke="rgba(255,120,60,0.6)"
            stroke-width="0.5"
            transform="translate({arrow.tip.x},{arrow.tip.y}) rotate({arrow.angle + 90})"
          />
        {/if}
      {/if}
    {/each}
  </svg>

  <!-- Scroll hint -->
  {#if scrollProgress < 0.04}
    <div class="hint">
      <span>scroll to grow the city</span>
      <div class="bounce">↓</div>
    </div>
  {/if}

  <!-- Progress badge -->
  {#if scrollProgress > 0.02}
    <div class="badge">+{Math.round(scrollProgress * 100)}%</div>
  {/if}
</div>

<style>
  .scene {
    position: sticky;
    top: 0;
    width: 100%;
    height: 100vh;
    overflow: hidden;
  }

  .skyline-svg {
    width: 100%;
    height: 100%;
    display: block;
  }

  .hint {
    position: absolute;
    bottom: 2rem;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.25rem;
    pointer-events: none;
    animation: bob 2s ease-in-out infinite;
  }

  .hint span {
    font-family: 'Courier New', monospace;
    font-size: 0.65rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: rgba(176, 196, 222, 0.4);
  }

  .bounce { color: #ff4500; font-size: 0.9rem; }

  @keyframes bob {
    0%, 100% { opacity: 0.4; transform: translateX(-50%) translateY(0); }
    50%       { opacity: 1;   transform: translateX(-50%) translateY(5px); }
  }

  .badge {
    position: absolute;
    top: 1.2rem;
    right: 1.2rem;
    background: rgba(255, 69, 0, 0.08);
    border: 1px solid rgba(255, 69, 0, 0.35);
    color: #ff6b35;
    font-family: 'Courier New', monospace;
    font-size: 0.78rem;
    font-weight: bold;
    letter-spacing: 0.06em;
    padding: 0.2rem 0.6rem;
    border-radius: 999px;
    pointer-events: none;
  }
</style>

