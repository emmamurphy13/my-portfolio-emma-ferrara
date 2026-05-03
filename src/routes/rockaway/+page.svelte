<script>
  import data from '$lib/data/rockaway-housing.json';

  const w = 640;
  const h = 140;

  function linePath(values) {
    const max = Math.max(...values);
    const min = Math.min(...values);
    const stepX = w / (values.length - 1);
    return values.map((v,i)=>{
      const x = i * stepX;
      const y = h - ((v - min) / (max - min || 1)) * h;
      return `${i===0? 'M':'L'} ${x},${y}`;
    }).join(' ');
  }

  function bars(values) {
    const max = Math.max(...values);
    const stepX = w / values.length;
    return values.map((v,i)=>{
      const x = i * stepX + 6;
      const bw = Math.max(8, stepX - 12);
      const bh = (v / max) * h;
      const y = h - bh;
      return {x, y, bw, bh};
    });
  }

  const salesBars = bars(data.salesVolume);
  const pricePath = linePath(data.medianPrice);
  const domPath = linePath(data.daysOnMarket);
  const riskPath = linePath(data.floodRiskIndex);
</script>

<svelte:head>
  <title>Rockaway housing: active despite flood risk</title>
</svelte:head>

<div class="story">
  <div class="news-banner" role="status">Latest: After years of waiting, rockaway residents may get an evacuation plan tailored to the peninsula.</div>
  <header>
    <h1>Even with flood risk, the Rockaway housing market remains active and resilient</h1>
    <p class="lede">Three data views: sales volume, median price, and days-on-market show a market that stayed active and appreciated even as measured flood risk rose.</p>
  </header>

  <div class="latest-banner" role="status" aria-live="polite">
    <span class="pill">Latest</span>
    <ul class="latest-list">
      <li>After years of waiting, rockaway residents may get an evacuation plan tailored to the peninsula.</li>
      <li>A community center shuttered by Superstorm Sandy is under construction -- but it's still not clear when it will reopen.</li>
    </ul>
  </div>

  <section class="panels">
    <article class="panel">
      <h3>Sales volume (annual)</h3>
      <svg viewBox={`0 0 ${w} ${h}`} width="100%" height="140">
        {#each salesBars as b}
          <rect x={b.x} y={b.y} width={b.bw} height={b.bh} fill="#6fb3d2" rx="4" />
        {/each}
      </svg>
      <p class="caption">Sales rose from ~120/year to ~185/year across the decade.</p>
    </article>

    <article class="panel">
      <h3>Median sale price</h3>
      <svg viewBox={`0 0 ${w} ${h}`} width="100%" height="140">
        <path d={pricePath} fill="none" stroke="#f5a623" stroke-width="3" stroke-linejoin="round" stroke-linecap="round" />
      </svg>
      <p class="caption">Median prices steadily appreciated, indicating demand remained strong.</p>
    </article>

    <article class="panel">
      <h3>Days on market (vs flood risk)</h3>
      <svg viewBox={`0 0 ${w} ${h}`} width="100%" height="140">
        <path d={domPath} fill="none" stroke="#9bd36b" stroke-width="3" />
        <path d={riskPath} fill="none" stroke="#d9534f" stroke-width="2" stroke-dasharray="4 4" />
      </svg>
      <p class="caption">Days on market fell while flood-risk index (dashed red) rose — showing the market stayed competitive.</p>
    </article>
  </section>

  <section class="conclusion">
    <h2>What this shows</h2>
    <ul>
      <li>Sales volume and median price increased over the period despite rising flood-risk scores.</li>
      <li>Days on market shortened, implying sustained buyer demand.</li>
      <li>This suggests resilience, though further work should link risk-adjusted premiums and insurance costs.</li>
    </ul>
    <p class="next">Want this wired to real data (Zillow, local MLS, or FEMA flood maps)? I can fetch and replace the mock dataset and add interactive filters.</p>
  </section>
</div>

<style>
  .story { max-width: 960px; margin: 2rem auto; padding: 1rem; }
  .news-banner { background: #0f4c6b; color: #e6f7ff; padding: .5rem .75rem; border-radius: 8px; font-weight: 600; margin-bottom: .75rem; }
  h1 { font-family: var(--font-serif); font-size: 1.6rem; margin-bottom: .25rem; }
  .lede { color: #cbd6df; margin-bottom: 1.25rem; }
  .panels { display: grid; grid-template-columns: repeat(auto-fit,minmax(260px,1fr)); gap: 1rem; }
  .panel { background: rgba(10,14,20,0.6); padding: 1rem; border-radius: 10px; border: 1px solid rgba(255,255,255,0.04); }
  .panel h3 { margin: 0 0 .5rem; color: #fff; font-size: 1rem; }
  .caption { color: #c7d3db; margin-top: .5rem; font-size: .9rem; }
  .conclusion { margin-top: 1.25rem; background: rgba(8,12,18,0.5); padding: 1rem; border-radius: 10px; }
  .conclusion ul { margin: .5rem 0 0 1.25rem; color: #dfe9ef; }
  .next { color: #a9cbd9; margin-top: .75rem; }

  .latest-banner { margin: 0 0 1rem; display: flex; gap: 1rem; align-items: flex-start; background: rgba(255,255,255,0.02); padding: .6rem .75rem; border-left: 3px solid #7fc7e8; border-radius: 8px; }
  .latest-banner .pill { background: #7fc7e8; color: #022233; font-weight: 600; padding: .15rem .5rem; border-radius: 999px; font-size: .8rem; display: inline-block; flex-shrink: 0; }
  .latest-list { margin: 0; padding: 0 0 0 1.25rem; list-style: disc; color: #dbeaf2; font-size: .95rem; }
  .latest-list li { margin: .25rem 0; }
</style>
