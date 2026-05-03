<script>
  import { onMount, onDestroy } from 'svelte';

  function seededRand(seed) {
    let s = seed;
    return () => {
      s = (s * 16807) % 2147483647;
      return (s - 1) / 2147483646;
    };
  }

  const starRng = seededRand(99);
  const stars = Array.from({ length: 80 }, (_, i) => ({
    id: i,
    x: starRng() * 100,
    y: starRng() * 45,
    r: starRng() * 1.2 + 0.3,
    opacity: starRng() * 0.6 + 0.4,
    twinkle: starRng() * 3 + 1.5,
  }));

  const moon = { cx: 82, cy: 12, r: 4.5 };
  const spire = { x: 24.5, strokeW: 0.4 };

  const buildings = [
    { x: 0,  w: 10, startY: 62, startH: 38, endH: 20, color: '#04080f', windows: { cols: 2, rows: 7, ox: 1.5, oy: 1 } },
    { x: 8,  w: 8,  startY: 55, startH: 45, endH: 24, color: '#04080f', windows: { cols: 1, rows: 8, ox: 2, oy: 1 } },
    { x: 14, w: 12, startY: 58, startH: 42, endH: 28, color: '#04080f', windows: { cols: 2, rows: 7, ox: 1.5, oy: 1 } },
    { x: 24, w: 10, startY: 50, startH: 50, endH: 33, color: '#04080f', windows: { cols: 2, rows: 9, ox: 1.5, oy: 1 } },
    { x: 32, w: 14, startY: 60, startH: 40, endH: 37, color: '#04080f', windows: { cols: 2, rows: 6, ox: 2, oy: 1 } },
    { x: 44, w: 9,  startY: 54, startH: 46, endH: 41, color: '#04080f', windows: { cols: 1, rows: 8, ox: 3, oy: 1 } },
    { x: 51, w: 11, startY: 57, startH: 43, endH: 45, color: '#04080f', windows: { cols: 2, rows: 7, ox: 1.5, oy: 1 } },
    { x: 60, w: 12, startY: 52, startH: 48, endH: 49, color: '#04080f', windows: { cols: 2, rows: 8, ox: 1.5, oy: 1 } },
    { x: 70, w: 10, startY: 60, startH: 40, endH: 53, color: '#04080f', windows: { cols: 2, rows: 6, ox: 1.5, oy: 1 } },
    { x: 78, w: 13, startY: 55, startH: 45, endH: 58, color: '#04080f', windows: { cols: 2, rows: 7, ox: 2, oy: 1 } },
    { x: 89, w: 11, startY: 58, startH: 42, endH: 63, color: '#04080f', windows: { cols: 2, rows: 7, ox: 1.5, oy: 1 } },

    { x: 0,  w: 14, startY: 68, startH: 32, endH: 14, color: '#060c18', windows: { cols: 2, rows: 5, ox: 1.5, oy: 1.2 } },
    { x: 12, w: 10, startY: 64, startH: 36, endH: 17, color: '#060c18', windows: { cols: 2, rows: 6, ox: 1.5, oy: 1.2 } },
    { x: 20, w: 16, startY: 70, startH: 30, endH: 21, color: '#060c18', windows: { cols: 3, rows: 5, ox: 1.2, oy: 1.2 } },
    { x: 34, w: 11, startY: 65, startH: 35, endH: 26, color: '#060c18', windows: { cols: 2, rows: 5, ox: 1.5, oy: 1.2 } },
    { x: 43, w: 9,  startY: 68, startH: 32, endH: 29, color: '#060c18', windows: { cols: 1, rows: 5, ox: 2.5, oy: 1.2 } },
    { x: 50, w: 13, startY: 63, startH: 37, endH: 33, color: '#060c18', windows: { cols: 2, rows: 6, ox: 1.5, oy: 1.2 } },
    { x: 61, w: 15, startY: 67, startH: 33, endH: 37, color: '#060c18', windows: { cols: 2, rows: 5, ox: 2, oy: 1.2 } },
    { x: 74, w: 10, startY: 65, startH: 35, endH: 41, color: '#060c18', windows: { cols: 2, rows: 5, ox: 1.2, oy: 1.2 } },
    { x: 82, w: 18, startY: 70, startH: 30, endH: 46, color: '#060c18', windows: { cols: 3, rows: 4, ox: 1.5, oy: 1.5 } },

    { x: 0,  w: 16, startY: 74, startH: 26, endH: 11, color: '#020508', windows: { cols: 2, rows: 4, ox: 1.5, oy: 1.2 } },
    { x: 14, w: 12, startY: 72, startH: 28, endH: 13, color: '#020508', windows: { cols: 2, rows: 4, ox: 1.5, oy: 1.5 } },
    { x: 24, w: 18, startY: 76, startH: 24, endH: 15, color: '#020508', windows: { cols: 3, rows: 3, ox: 1.5, oy: 1.5 } },
    { x: 40, w: 14, startY: 73, startH: 27, endH: 19, color: '#020508', windows: { cols: 2, rows: 4, ox: 1.5, oy: 1.5 } },
    { x: 52, w: 12, startY: 75, startH: 25, endH: 22, color: '#020508', windows: { cols: 2, rows: 3, ox: 1.5, oy: 2 } },
    { x: 62, w: 16, startY: 71, startH: 29, endH: 25, color: '#020508', windows: { cols: 2, rows: 4, ox: 2, oy: 1.5 } },
    { x: 76, w: 13, startY: 74, startH: 26, endH: 28, color: '#020508', windows: { cols: 2, rows: 4, ox: 1.5, oy: 1.5 } },
    { x: 87, w: 13, startY: 76, startH: 24, endH: 31, color: '#020508', windows: { cols: 2, rows: 3, ox: 1.5, oy: 2 } },
  ];

  function getWindows(b, topY) {
    const rng = seededRand(Math.round(b.x * 7 + b.w * 13 + b.startY));
    const wins = [];
    const ww = 1.2, wh = 1.4;
    const { cols, rows, ox, oy } = b.windows;
    const totalW = cols * ww + (cols - 1) * ox;
    const startX = b.x + (b.w - totalW) / 2;
    for (let r = 0; r < rows; r++) {
      for (let c = 0; c < cols; c++) {
        const wy = topY + 2 + r * (wh + oy);
        if (wy + wh > 99) continue;
        const lit = rng() > 0.55;
        wins.push({
          x: startX + c * (ww + ox),
          y: wy,
          w: ww,
          h: wh,
          color: lit ? (rng() > 0.3 ? '#f5d76e' : '#a8d8f0') : 'rgba(255,255,255,0.02)',
        });
      }
    }
    return wins;
  }

  let progress = 0;

  function handleScroll() {
    if (typeof window === 'undefined' || typeof document === 'undefined') return;
    const max = document.documentElement.scrollHeight - window.innerHeight;
    progress = max > 0 ? Math.min(1, window.scrollY / max) : 0;
  }

  onMount(() => {
    if (typeof window === 'undefined') return;
    window.addEventListener('scroll', handleScroll, { passive: true });
    handleScroll();
  });
  onDestroy(() => {
    if (typeof window !== 'undefined') window.removeEventListener('scroll', handleScroll);
  });

  function easeInOutCubic(t) { return t < 0.5 ? 4 * t * t * t : 1 - Math.pow(-2 * t + 2, 3) / 2; }
  function easeOutQuart(t) { return 1 - Math.pow(1 - t, 4); }

  function buildingT(index, total, ep) {
    const stagger = 0.2;
    const start = (index / total) * stagger;
    const local = Math.max(0, Math.min(1, (ep - start) / (1 - stagger)));
    return easeOutQuart(local);
  }

  $: ep = easeInOutCubic(progress);

  $: liveBuildings = buildings.map((b, i) => {
    const t = buildingT(i, buildings.length - 1, ep);
    const h = b.startH + (b.endH - b.startH) * t;
    const topY = 100 - h;
    return { ...b, h, topY, wins: getWindows(b, topY) };
  });

  $: bgLayer = liveBuildings.slice(0, 11);

  const FLOAT_ABOVE = [18, 14, 15, 12, 13, 11, 12, 10, 9, 8, 7];

  $: arrowPts = bgLayer.map((b, i) => ({ x: b.x + b.w / 2, y: b.topY - FLOAT_ABOVE[i] }));

  $: arrowPath = (() => {
    if (arrowPts.length < 2) return '';
    return arrowPts.map((p, i) => `${i === 0 ? 'M' : 'L'} ${p.x} ${p.y}`).join(' ');
  })();

  const ARROW_HEAD_BACKOFF = 3.4;

  $: arrowShaftPts = (() => {
    if (arrowPts.length < 2) return arrowPts;
    const prev = arrowPts[arrowPts.length - 2];
    const tip = arrowPts[arrowPts.length - 1];
    const dx = tip.x - prev.x;
    const dy = tip.y - prev.y;
    const len = Math.hypot(dx, dy);
    if (!len) return arrowPts;
    const cut = Math.min(ARROW_HEAD_BACKOFF, len * 0.6);
    const end = { x: tip.x - (dx / len) * cut, y: tip.y - (dy / len) * cut };
    return [...arrowPts.slice(0, -1), end];
  })();

  $: arrowShaftPath = (() => {
    if (arrowShaftPts.length < 2) return '';
    return arrowShaftPts.map((p, i) => `${i === 0 ? 'M' : 'L'} ${p.x} ${p.y}`).join(' ');
  })();

  $: arrowTip = (() => {
    const a = arrowPts[arrowPts.length - 2];
    const b = arrowPts[arrowPts.length - 1];
    if (!a || !b) return { x: 95, y: 30, angle: 0 };
    const angle = Math.atan2(b.y - a.y, b.x - a.x) * (180 / Math.PI);
    return { x: b.x, y: b.y, angle };
  })();

  $: arrowOpacity = Math.min(1, ep * 4);

  const diamonds = Array.from({ length: 12 }, (_, i) => {
    const r = seededRand(i * 41 + 3);
    return { x: r() * 86 + 6, y: r() * 52 + 16, size: r() * 1.8 + 0.7 };
  });
</script>

<div class="skyline-bg">
  <svg viewBox="0 0 100 100" preserveAspectRatio="xMidYMid slice" xmlns="http://www.w3.org/2000/svg" aria-label="Night city skyline">
    <defs>
      <linearGradient id="skyGrad" x1="0" y1="0" x2="0" y2="1">
        <stop offset="0%"   stop-color="#0d1f3c" />
        <stop offset="40%"  stop-color="#112244" />
        <stop offset="75%"  stop-color="#162a50" />
        <stop offset="100%" stop-color="#1c3260" />
      </linearGradient>
      <linearGradient id="arrowGrad" x1="0%" y1="0%" x2="100%" y2="0%">
        <stop offset="0%"   stop-color="#1a2a6c" />
        <stop offset="45%"  stop-color="#c94b4b" />
        <stop offset="100%" stop-color="#e8001e" />
      </linearGradient>
      <filter id="arrowGlow">
        <feGaussianBlur stdDeviation="1.0" result="blur"/>
        <feMerge><feMergeNode in="blur"/><feMergeNode in="SourceGraphic"/></feMerge>
      </filter>
      <filter id="tipGlow">
        <feGaussianBlur stdDeviation="0.7" result="blur"/>
        <feMerge><feMergeNode in="blur"/><feMergeNode in="SourceGraphic"/></feMerge>
      </filter>
    </defs>

    <rect width="100" height="100" fill="url(#skyGrad)" />

    {#each stars as s}
      <circle cx={s.x} cy={s.y} r={s.r} fill="white" opacity={s.opacity}>
        <animate attributeName="opacity" values={`${s.opacity};${s.opacity * 0.3};${s.opacity}`} dur={s.twinkle + 's'} repeatCount="indefinite" />
      </circle>
    {/each}

    <circle cx={moon.cx} cy={moon.cy} r={moon.r + 1.5} fill="#fef3b4" opacity="0.15" />
    <circle cx={moon.cx} cy={moon.cy} r={moon.r} fill="#fef3b4" opacity="0.95" />
    <circle cx={moon.cx - 1.2} cy={moon.cy - 0.8} r={moon.r} fill="#112244" opacity="0.85" />

    <line x1={spire.x} y1={liveBuildings[3].topY} x2={spire.x} y2={liveBuildings[3].topY + 6} stroke="#04080f" stroke-width={spire.strokeW} />

    {#each liveBuildings as b}
      <rect x={b.x} y={b.topY} width={b.w} height={100 - b.topY} fill={b.color} />
      {#each b.wins as w}
        <rect x={w.x} y={w.y} width={w.w} height={w.h} fill={w.color} rx="0.15" />
      {/each}
    {/each}

    <rect x="0" y="98" width="100" height="2" fill="#020408" />

    {#if ep > 0.005}
      <g opacity={arrowOpacity}>
        <path d={arrowShaftPath} fill="none" stroke="url(#arrowGrad)" stroke-width="2.8" stroke-linecap="square" stroke-linejoin="miter" opacity="0.22" filter="url(#arrowGlow)" />
        <path d={arrowShaftPath} fill="none" stroke="url(#arrowGrad)" stroke-width="1.6" stroke-linecap="square" stroke-linejoin="miter" />
      </g>

      {#each diamonds as d}
        {@const dOp = Math.max(0, Math.min(1, (ep - d.x / 100 + 0.08) * 12)) * arrowOpacity * 0.85}
        {#if dOp > 0.01}
          <g transform={`translate(${d.x},${d.y}) rotate(45)`} opacity={dOp}>
            <rect x={-d.size / 2} y={-d.size / 2} width={d.size} height={d.size} fill={d.x % 3 === 0 ? '#e8001e' : d.x % 3 === 1 ? '#3a6fd8' : '#ffffff'} />
          </g>
        {/if}
      {/each}

      <g transform={`translate(${arrowTip.x},${arrowTip.y}) rotate(${arrowTip.angle})`} opacity={arrowOpacity} filter="url(#tipGlow)">
        <polygon points="4,0 -3,-2.8 -3,2.8" fill="#e8001e" />
        <polygon points="4,0 -3,0 -3,2.8" fill="#8b0000" />
        <polygon points="4,0 -3,-2.8 -3,0" fill="#ff4040" />
      </g>
    {/if}
  </svg>
</div>

<style>
  .skyline-bg {
    position: fixed;
    inset: 0;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    pointer-events: none;
    overflow: hidden;
  }

  .skyline-bg svg {
    width: 100%;
    height: 100%;
    display: block;
  }
</style>
