<script>
  import { base } from '$app/paths';
</script>

<svelte:head>
  <title>Emma Murphy — Resume</title>
</svelte:head>

<div class="resume-page">
  <div class="background-dim" aria-hidden="true"></div>

  <div class="resume-content">
    <header class="resume-header">
      <a href="{base}/" class="back-link">← Back to Portfolio</a>
      <h1>Emma Murphy</h1>
      <p class="subtitle">Economics Reporter · Data Enthusiast</p>
    </header>

    <div class="resume-paper">
      <img src="{base}/photos/resume-page.png" alt="Emma Murphy resume" class="resume-image" />
    </div>

    <footer class="resume-footer">
      <a href="{base}/Emma_Murphy_Resume.pdf" class="download-btn" download>
        Download Resume (PDF)
      </a>
    </footer>
  </div>
</div>

<style>
  .resume-page {
    min-height: 100vh;
    width: 100%;
    background: linear-gradient(180deg, rgba(6,10,18,0.98) 0%, rgba(8,12,20,0.96) 100%);
    position: relative;
  }

  .background-dim {
    position: fixed;
    inset: 0;
    pointer-events: none;
    background: linear-gradient(180deg, rgba(6,10,18,0.02) 0%, rgba(6,10,18,0.08) 100%);
    z-index: 0;
  }

  .resume-content {
    position: relative;
    z-index: 2;
    max-width: 100%;
    margin: 0 auto;
    padding: 0;
    display: flex;
    flex-direction: column;
    height: 100vh;
  }

  .resume-header {
    padding: 1.25rem 2rem 1rem;
    text-align: center;
    flex-shrink: 0;
    position: relative;
  }

  .resume-header::after {
    content: '';
    display: block;
    width: 84px;
    height: 3px;
    margin: 1rem auto 0;
    border-radius: 999px;
    background: #f5d76e;
    box-shadow: 0 0 18px rgba(245, 215, 110, 0.18);
  }

  .back-link {
    display: inline-block;
    color: #a8d8f0;
    text-decoration: none;
    font-size: 0.95rem;
    padding: 0.5rem 0;
    transition: color 200ms ease;
    margin-bottom: 1rem;
  }

  .back-link:hover {
    color: #f5d76e;
  }

  .resume-header h1 {
    font-family: Georgia, serif;
    font-size: 2.5rem;
    color: var(--color-white, #ffffff);
    margin: 0.5rem 0 0.25rem;
    letter-spacing: -0.02em;
  }

  .subtitle {
    color: #a8d8f0;
    font-size: 1.1rem;
    margin: 0.5rem 0;
    font-weight: 500;
  }


  .resume-paper {
    flex: 1;
    width: 100%;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 0.75rem 1rem;
    gap: 1rem;
    background: transparent;
  }

  .resume-image {
    width: min(84vw, 760px);
    max-height: calc(100vh - 16rem);
    max-width: 100%;
    height: auto;
    object-fit: contain;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
    display: block;
  }
  .resume-footer {
    display: flex;
    justify-content: center;
    padding: 1rem 1.5rem 1.25rem;
    flex-shrink: 0;
  }

  .download-btn {
    display: inline-block;
    background: #7fc7e8;
    border: 1px solid #f5d76e;
    color: #022233;
    padding: 0.75rem 1.5rem;
    border-radius: 8px;
    text-decoration: none;
    font-weight: 600;
    font-size: 1rem;
    transition: transform 200ms ease, box-shadow 200ms ease;
  }

  .download-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 16px rgba(127, 199, 232, 0.32);
    background: #8fd0ea;
  }
</style>
