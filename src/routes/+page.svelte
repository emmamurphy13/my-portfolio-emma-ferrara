<!--
@component
Portfolio homepage with cartoon city skyline background
-->
<script>
  import PortfolioHomepage from '$lib/components/Portfolio/PortfolioHomepage.svelte';
  import portfolioData from '$lib/data/portfolio-data.yaml';

  let pageTitle = 'Portfolio';
</script>

<!-- This sets the page title in the browser tab -->
<svelte:head>
  <title>{pageTitle}</title>
  <meta
    name="description"
    content="My portfolio of data journalism and interactive projects."
  />
</svelte:head>

<!-- Portfolio page with cartoon city skyline background -->
<PortfolioHomepage data={portfolioData} />
