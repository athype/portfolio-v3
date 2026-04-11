<script>
    import { onMount } from 'svelte';
    import { gsap } from 'gsap';

    export let title;
    export let description;
    export let image = null;
    export let tags;
    export let link;
    export let index;

    $: screenshotSrc = image || (link !== '#'
        ? `https://api.microlink.io/?url=${encodeURIComponent(link)}&screenshot=true&meta=false&embed=screenshot.url`
        : null);

    let projectRef;

    onMount(() => {
        gsap.from(projectRef, {
            opacity: 0,
            y: 50,
            duration: 0.8,
            delay: 0.1 * index,
            scrollTrigger: {
                trigger: projectRef,
                start: 'top 85%'
            }
        });
    });
</script>

<div class="project" bind:this={projectRef}>
    <div class="project-image">
        {#if screenshotSrc}
            <img src={screenshotSrc} alt={title} loading="lazy" />
        {:else}
            <div class="image-placeholder"></div>
        {/if}
        <div class="image-overlay"></div>
    </div>

    <div class="project-content">
        <h3>{title}</h3>
        <p>{description}</p>

        <ul class="project-tags">
            {#each tags as tag}
                <li>{tag}</li>
            {/each}
        </ul>

        <a href={link} class="project-link" target="_blank" rel="noopener noreferrer">
            View Project →
        </a>
    </div>
</div>

<style>
    .project {
        display: flex;
        flex-direction: column;
        height: 100%;
        gap: 2rem;
    }

    h3 {
        font-size: 1.8rem;
        margin-bottom: 1rem;
    }

    p {
        font-size: 1rem;
        color: var(--text-secondary);
        margin-bottom: 1.5rem;
        line-height: 1.7;
    }

    .project-tags {
        display: flex;
        flex-wrap: wrap;
        list-style: none;
        margin-bottom: 1.5rem;
        gap: 0.5rem;
    }

    .project-tags li {
        padding: 0.4rem 1rem;
        background-color: var(--bg-secondary);
        border-radius: 2rem;
        font-size: 0.85rem;
    }

    .project-link {
        font-weight: 500;
        display: inline-block;
        border-bottom: 2px solid var(--accent);
        padding-bottom: 0.25rem;
        transition: color 0.2s ease;
    }

    .project-link:hover {
        color: var(--accent);
    }

    .project-image {
        position: relative;
        border-radius: var(--border-radius);
        overflow: hidden;
        aspect-ratio: 16/9;
        flex-shrink: 0;
    }

    .project-image img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        transition: transform 0.5s ease;
    }

    .image-placeholder {
        width: 100%;
        height: 100%;
        background-color: var(--surface, #1a1a1e);
    }

    .image-overlay {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(13, 13, 15, 0.2);
        transition: background-color 0.3s ease;
    }

    .project:hover .project-image img {
        transform: scale(1.05);
    }

    .project:hover .image-overlay {
        background-color: rgba(13, 13, 15, 0);
    }
</style>