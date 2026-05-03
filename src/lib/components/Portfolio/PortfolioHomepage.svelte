I<!--
@component
PortfolioHomepage.svelte — A portfolio page with cartoon city skyline background.

Displays a personal profile and work samples overlaid on a colorful cartoon
city skyline background.
-->
<script>
  import { base } from '$app/paths';
  import NightSkyline from '$lib/components/Portfolio/NightSkyline.svelte';
  import Profile from '$lib/components/Portfolio/Profile.svelte';
  import Card from '$lib/components/Data/Card.svelte';
  import CardGrid from '$lib/components/Data/CardGrid.svelte';

  let { data = null } = $props();
</script>

<div class="portfolio-page">
  <div style="position: absolute; inset: 0; z-index: 1;">
    <NightSkyline />
  </div>
  <div class="background-dim" aria-hidden="true"></div>

  <div class="portfolio-content">
    <div class="container">
      {#if data}
        <div class="profile-section">
          <Profile
            name={data.name}
            tagline={data.tagline}
            photo={data.photo}
            photoFull={data.photoFull}
            photoAlt={data.photoAlt}
            email={data.email}
            github={data.github}
            linkedin={data.linkedin}
            bio={data.bio}
          />
        </div>

        <div class="clips-section">
          <h2>Economics Stories</h2>

          <CardGrid>
            {#each data.clips as clip (clip.title)}
              <div class="story-card-wrap">
                <Card href={clip.href} image={clip.image} imageAlt={clip.title}>
                  <h3>{clip.title}</h3>
                  <p>{clip.description}</p>
                </Card>

                <div class="story-hover-box" role="note" aria-live="polite">
                  {clip.description}
                </div>
              </div>
            {/each}
          </CardGrid>
        </div>

        {#if data.localNews}
          <div class="clips-section">
            <h2>Local News</h2>

            <CardGrid>
              {#each data.localNews as clip (clip.title)}
                <div class="story-card-wrap">
                  <Card href={clip.href} image={clip.image} imageAlt={clip.title}>
                    <h3>{clip.title}</h3>
                    <p>{clip.description}</p>
                  </Card>

                  <div class="story-hover-box" role="note" aria-live="polite">
                    {clip.description}
                  </div>
                </div>
              {/each}
            </CardGrid>
          </div>
        {/if}

        <div class="resume-section">
          <a href="{base}/resume" class="resume-btn">View My Resume</a>
        </div>
      {/if}
    </div>
  </div>
</div>

<style lang="scss">
  .portfolio-page {
    position: relative;
    min-height: 100vh;
    width: 100%;
  }

  .background-dim {
    position: fixed;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    background: linear-gradient(180deg, rgba(6,10,18,0.02) 0%, rgba(6,10,18,0.08) 100%);
  }

  .portfolio-content {
    position: relative;
    z-index: 2;
    padding: calc(var(--spacing-xl) + 2rem) 0 6rem;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    min-height: calc(100vh - 120px);
  }

  .container {
    max-width: var(--max-width-wide);
    margin: 0 auto;
    padding: 0 var(--spacing-md);
    display: flex;
    flex-direction: column;
    gap: var(--spacing-xl);
    align-items: center;
  }

  .profile-section {
    width: 100%;
    max-width: 640px;
    margin-bottom: var(--spacing-sm);
    transform: translateY(-10rem);
    background: rgba(10,14,20,0.54);
    padding: var(--spacing-md) var(--spacing-md);
    border-radius: 12px;
    border: 1px solid rgba(255,255,255,0.06);
    box-shadow: 0 10px 30px rgba(2,6,23,0.45);
    backdrop-filter: blur(4px);
    display: block;
    -webkit-backdrop-filter: blur(4px);
  }

    .clips-section {
      width: 100%;

      /* polka-dot background behind images */
      background-image: radial-gradient(rgba(255,255,255,0.03) 2px, transparent 2px);
      background-size: 28px 28px;
      /* keep top padding but remove bottom padding so sections sit flush */
/* keep some bottom padding so hover descriptions have room */
      padding: var(--spacing-lg) var(--spacing-md) var(--spacing-md);
      border-radius: 12px;

    h2 {
      font-family: var(--font-serif);
      font-size: var(--font-size-5xl);
      font-weight: var(--font-weight-semibold);
      margin: 0 0 var(--spacing-md);
      text-align: left;
      color: var(--color-white);
    }

    /* Reduce vertical space between two clips-section rows */
    /* cancel the first section's top padding so the two sections touch */
    & + .clips-section {
      /* reduce the negative overlap so there's a small gap for hover descriptions */
      margin-top: calc(var(--spacing-lg) * -0.6);
    }

    .card-grid {
      width: 100%;
    }

    .story-card-wrap {
      display: flex;
      flex-direction: column;
      gap: var(--spacing-xs);
      position: relative;
    }

    .story-hover-box {
      position: absolute;
      left: 0;
      top: calc(100% + 0.75rem);
      z-index: 30;
      width: min(540px, 100%);
      opacity: 0;
      transform: translateY(-6px);
      transition: opacity 180ms ease, transform 180ms ease;
      pointer-events: none;

      background: rgba(8, 16, 30, 0.95);
      border: 1px solid rgba(168, 216, 240, 0.45);
      border-radius: 10px;
      color: var(--color-white);
      font-family: var(--font-serif);
      font-size: var(--font-size-sm);
      line-height: var(--leading-caption);
      padding: var(--spacing-sm);
      box-shadow: 0 8px 28px rgba(2, 6, 23, 0.48);
    }

    .story-card-wrap:hover .story-hover-box,
    .story-card-wrap:focus-within .story-hover-box {
      opacity: 1;
      transform: translateY(0);
      pointer-events: auto;
    }
  }

  .resume-section {
    width: 100%;
    display: flex;
    justify-content: center;
    margin-top: var(--spacing-xl);
    padding: var(--spacing-lg) var(--spacing-md);
  }

  .resume-btn {
    display: inline-block;
    background: linear-gradient(135deg, #7fc7e8 0%, #6fb3d2 100%);
    color: #022233;
    padding: 0.75rem 1.75rem;
    border-radius: 8px;
    text-decoration: none;
    font-weight: 600;
    font-size: 1rem;
    transition: transform 200ms ease, box-shadow 200ms ease;
  }

  .resume-btn:hover {
    transform: translateY(-3px);
    box-shadow: 0 8px 20px rgba(127,199,232,0.35);
  }
</style>
