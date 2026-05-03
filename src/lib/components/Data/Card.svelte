<!--
@component
Card.svelte — A card container with optional image, link, and footer actions.
-->
<script>
  import { asset } from '$app/paths';

  let { href = '', image = '', imageAlt = '', children, footer } = $props();

  // Resolve local images (those starting with /) using asset()
  // but not external URLs (http://, https://, //, data:)
  const resolvedImage = $derived(
    image && image.startsWith('/') && !image.startsWith('//')
      ? asset(image)
      : image
  );
</script>

{#snippet cardContent()}
  {#if image}
    <div class="card-image">
      <img src={resolvedImage} alt={imageAlt} />
    </div>
  {/if}

  <div class="card-body">
    {@render children()}
  </div>

  {#if footer}
    <div class="card-footer">
      {@render footer()}
    </div>
  {/if}
{/snippet}

{#if href}
  <a {href} class="card card-link">
    {@render cardContent()}
  </a>
{:else}
  <div class="card">
    {@render cardContent()}
  </div>
{/if}

<style lang="scss">
  .card {
    border: 0;
    border-radius: 12px;
    background: transparent;
    overflow: hidden;
    display: block;
    height: 100%;
    transition: transform 180ms ease, box-shadow 180ms ease;
    will-change: transform;
  }

  .card-link {
    display: block;
    color: inherit;
    text-decoration: none;
    transition: transform 0.18s ease, box-shadow 0.18s ease;

    &:hover {
      transform: translateY(-6px) scale(1.02);
      box-shadow: 0 18px 40px rgba(2,6,23,0.28);
    }
  }

  .card-image {
    width: 100%;
    height: 220px;
    overflow: hidden;

    img {
      display: block;
      width: 100%;
      height: 100%;
      object-fit: cover;
      transition: transform 300ms ease;
    }

    :global(.card-link:hover) & img {
      transform: scale(1.04);
    }
  }

  .card-body {
    position: relative;
    padding: var(--spacing-md, 1rem);
    color: var(--color-white);
    margin-top: -56px;
    z-index: 2;

    background: linear-gradient(180deg, rgba(0,0,0,0.0), rgba(0,0,0,0.35));
    backdrop-filter: blur(2px);

    :global(h3) {
      font-size: 1.05rem;
      margin: 0 0 0.25rem;
      line-height: 1.2;
      color: var(--color-white);
    }

    :global(p) {
      display: none;
    }
  }

  .card-footer {
    display: none;
  }
</style>
