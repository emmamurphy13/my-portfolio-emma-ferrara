<!--
@component
ProfileBio.svelte — Reusable paragraph block for Profile bio text.

Accepts a string and renders paragraphs separated by blank lines.
-->
<script>
  let { text = '' } = $props();

  let paragraphs = $derived(text ? text.trim().split('\n\n') : []);
</script>

{#if paragraphs.length > 0}
  <div class="now-next">
    {#each paragraphs as para, i (i)}
      <p>{para}</p>
    {/each}
  </div>
{/if}

<style lang="scss">
  .now-next {
    margin-top: var(--spacing-xs);
  }

  .now-next p {
    line-height: 1.5;
    margin: 0 0 calc(var(--spacing-xs) * 1.15);
    max-width: 46rem;
    font-size: clamp(0.95rem, 1.4vw, 1.05rem);
    font-family: var(--font-serif);
    color: var(--color-white);
  }
</style>
