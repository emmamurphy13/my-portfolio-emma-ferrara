<!--
@component
Profile.svelte — A personal portfolio profile card.

Displays a headshot, name, tagline, contact links, and an optional
"now / next" bio section. Intended as the hero block of a portfolio page.

USAGE EXAMPLE:
<Profile
  name="Jane Smith"
  tagline="Data journalist covering housing and inequality"
  photo="/photos/jane-smith.jpg"
  email="jane@example.com"
  github="janesmith"
  linkedin="janesmith"
  bio="Currently reporting on evictions for The City.

Next, I'm exploring machine learning tools for document analysis."
/>
-->
<script>
  import { asset } from '$app/paths';
  import IconEmail from '$lib/components/Icons/IconEmail.svelte';
  import IconGitHub from '$lib/components/Icons/IconGitHub.svelte';
  import IconLinkedIn from '$lib/components/Icons/IconLinkedIn.svelte';
  import ProfileContactLink from './ProfileContactLink.svelte';
  import ProfileBio from './ProfileBio.svelte';

  let { name, tagline, photo, photoAlt, email, github, linkedin, bio, photoFull } =
    $props();

  const DEFAULT_PHOTO = '/photos/Screenshot 2026-05-02 at 8.14.31 PM.png';

  const resolvedPhoto = $derived(
    photo
      ? photo.startsWith && photo.startsWith('/') && !photo.startsWith('//')
        ? asset(photo)
        : photo
      : asset(DEFAULT_PHOTO)
  );

  const contacts = $derived(
    [
      email
        ? {
            href: `mailto:${email}`,
            label: 'Email',
            external: false,
            icon: IconEmail,
          }
        : null,
      github
        ? {
            href: `https://github.com/${github}`,
            label: 'GitHub',
            external: true,
            icon: IconGitHub,
          }
        : null,
      linkedin
        ? {
            href: `https://linkedin.com/in/${linkedin}`,
            label: 'LinkedIn',
            external: true,
            icon: IconLinkedIn,
          }
        : null,
    ].filter(Boolean)
  );
</script>

<section class="profile">
  <div class="profile-hero">
    <div class="hero-copy">
      <h1>{name}</h1>

      <div class="links-block">
        <ul class="contact">
          {#each contacts as contact (contact.label)}
            <ProfileContactLink
              href={contact.href}
              label={contact.label}
              external={contact.external}
              icon={contact.icon}
            />
          {/each}
        </ul>

        {#if tagline}
          <p class="tagline">{tagline}</p>
        {/if}
      </div>

      {#if photo}
        <div class="hero-photo-wrap" class:hero-photo-uncropped={resolvedPhoto && resolvedPhoto.includes('Screenshot 2026-05-02 at 8.14.31 PM.png')}>
          <img
            class="avatar-image"
            class:avatar-image--full={photoFull}
            class:avatar-uncropped={resolvedPhoto && resolvedPhoto.includes('Screenshot 2026-05-02 at 8.14.31 PM.png')}
            src={resolvedPhoto}
            alt={photoAlt ?? name}
            loading="lazy"
          />
        </div>
      {/if}

      <div class="bio-wrap">
        <ProfileBio text={bio} />
      </div>
    </div>
  </div>
</section>

<style lang="scss">
  @use '$lib/styles' as *;

  .profile {
    margin-bottom: var(--spacing-sm);
    padding: var(--spacing-xs) 0;
  }

  .profile-hero {
    display: block;
  }

  /*
    Two-column grid:
      col 1 = left content (name, then contacts+tagline+bio stacked)
      col 2 = photo (fixed width)

    Three rows:
      row 1 = name + photo top (photo spans rows 1–2)
      row 2 = contacts + tagline (bottom-aligned with photo bottom)
      row 3 = bio (full width)
  */
  .hero-copy {
    display: grid;
    grid-template-columns: minmax(0, 1fr) auto;
    grid-template-rows: 1fr auto auto;
    column-gap: var(--spacing-lg);
    /* photo height drives the row heights */
    grid-template-areas:
      "name    photo"
      "links   photo"
      "bio     bio";
  }

  h1 {
    grid-area: name;
    align-self: start;
    font-size: clamp(3.1rem, 7.2vw, 5.1rem);
    line-height: var(--leading-tight);
    margin: 0;
    letter-spacing: var(--letter-spacing-tight);
    color: var(--color-accent-warm);
    text-shadow: 0 6px 18px rgba(0,0,0,0.45);

    @include mobile {
      font-size: clamp(2.25rem, 8vw, 4rem);
    }
  }

  /* Photo spans both name + links rows so its bottom aligns with the links */
  .hero-photo-wrap {
    grid-area: photo;
    align-self: stretch;   /* fills both rows */
    justify-self: end;
    width: 200px;
    height: 270px;
    border-radius: 12px;
    overflow: hidden;
    flex: 0 0 auto;
    box-shadow: 0 8px 28px rgba(2,6,23,0.35);
    border: 1px solid rgba(255,255,255,0.06);
    background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    position: relative;
  }

  /* Links row: contacts + tagline sit together, bottom-aligned with photo */
  .links-block {
    grid-area: links;
    align-self: end;       /* pushes to bottom of the photo's height */
    display: flex;
    flex-direction: column;
    gap: 0;
  }

  .contact {
    list-style: none;
    padding: 0;
    margin: 0 0 var(--spacing-sm);
    display: flex;
    flex-wrap: wrap;
    gap: var(--spacing-sm);
  }

  .tagline {
    margin: 0;
    font-size: var(--font-size-lg);
    color: #a8d8f0;
    line-height: var(--leading-caption);
  }

  .bio-wrap {
    grid-area: bio;
    width: 100%;
    margin-top: var(--spacing-xs);
  }

  /* Photo image styles */
  .hero-photo-uncropped {
    width: 200px;
    height: 270px;
    border-radius: 12px;
    overflow: hidden;
    background: transparent;
  }

  .hero-photo-wrap :global(.image-figure) {
    margin: 0;
    height: 100%;
    width: 100%;
  }

  .hero-photo-wrap :global(.image) {
    display: block;
    width: 100%;
    height: 100%;
    aspect-ratio: 1 / 1;
    object-fit: cover;
    margin: 0;
    object-position: 35% 42%;
    transform: none;
    background: var(--color-light-gray);
  }

  .hero-photo-wrap .avatar-image {
    position: absolute;
    top: 50%;
    left: 0;
    height: 170%;
    width: auto;
    display: block;
    object-fit: cover;
    object-position: 35% 42%;
    transform: translate(-35%, -50%) scale(1.18);
    transform-origin: left center;
    will-change: transform;
  }

  .hero-photo-uncropped .avatar-image.avatar-uncropped {
    position: absolute;
    top: 50%;
    left: 0;
    width: 100%;
    height: 100%;
    max-width: 100%;
    display: block;
    object-fit: cover;
    object-position: center;
    transform: translateY(-50%);
  }

  .hero-photo-wrap .avatar-image,
  .hero-photo-uncropped .avatar-image.avatar-uncropped {
    border: 3px solid var(--color-accent-warm);
    border-radius: 8px;
    box-shadow: 0 2px 6px rgba(0,0,0,0.15);
  }

  .avatar-image--full {
    width: 200px;
    height: auto;
    display: block;
    object-fit: contain;
    object-position: 35% 42%;
    transform: none;
    will-change: auto;
    border-radius: 8px;
    overflow: visible;
  }

  @include mobile {
    .hero-copy {
      grid-template-columns: 1fr;
      grid-template-areas:
        "name"
        "photo"
        "links"
        "bio";
    }

    .hero-photo-wrap {
      justify-self: start;
      width: 180px;
      height: 240px;
    }

    .hero-photo-wrap .avatar-image {
      height: 150%;
      transform: translate(-28%, -50%) scale(1.12);
      object-position: 35% 42%;
      transform-origin: left center;
    }

    .avatar-image--full {
      width: 160px;
    }

    .hero-photo-wrap :global(.image) {
      object-position: 50% 50%;
    }
  }
</style>
