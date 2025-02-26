<script>
    import { onMount } from 'svelte';
    import { gsap } from 'gsap';

    export let title;
    export let description;
    export let image;
    export let tags;
    export let link;
    export let index;

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

    <div class="project-image">
        <img src={image} alt={title} loading="lazy" />
        <div class="image-overlay"></div>
    </div>
</div>

<style>
    .project {
        display: grid;
        grid-template-columns: 1fr 1.5fr;
        gap: 3rem;
        align-items: center;
    }

    .project:nth-child(even) {
        grid-template-columns: 1.5fr 1fr;
    }

    .project:nth-child(even) .project-content {
        order: 2;
    }

    .project:nth-child(even) .project-image {
        order: 1;
    }

    h3 {
        font-size: 2rem;
        margin-bottom: 1.5rem;
    }

    p {
        font-size: 1.1rem;
        color: var(--text-secondary);
        margin-bottom: 2rem;
        line-height: 1.7;
    }

    .project-tags {
        display: flex;
        flex-wrap: wrap;
        list-style: none;
        margin-bottom: 2rem;
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
    }

    .project-image img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        transition: transform 0.5s ease;
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

    @media (max-width: 920px) {
        .project,
        .project:nth-child(even) {
            grid-template-columns: 1fr;
        }

        .project-content,
        .project:nth-child(even) .project-content {
            order: 2;
        }

        .project-image,
        .project:nth-child(even) .project-image {
            order: 1;
        }
    }
</style>