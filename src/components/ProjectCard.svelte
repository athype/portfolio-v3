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

    function handleMouseMove(e) {
        const rect = projectRef.getBoundingClientRect();
        const x = (e.clientX - rect.left) / rect.width;
        const y = (e.clientY - rect.top) / rect.height;
        const tiltX = (y - 0.5) * -12;
        const tiltY = (x - 0.5) * 12;

        gsap.to(projectRef, {
            rotateX: tiltX,
            rotateY: tiltY,
            duration: 0.4,
            ease: 'power2.out',
            transformPerspective: 800,
        });
    }

    function handleMouseLeave() {
        gsap.to(projectRef, {
            rotateX: 0,
            rotateY: 0,
            duration: 0.7,
            ease: 'elastic.out(1, 0.5)',
        });
    }

    onMount(() => {
        // Add revealed class after a short stagger based on index
        setTimeout(() => {
            projectRef.classList.add('revealed');
        }, 300 + (index * 200));

        gsap.from(projectRef, {
            opacity: 0,
            y: 50,
            duration: 0.8,
            delay: 0.1 * index,
            scrollTrigger: {
                trigger: projectRef,
                start: 'top 85%',
                onEnter: () => projectRef.classList.add('revealed'),
            }
        });
    });
</script>

<div class="project" bind:this={projectRef} on:mousemove={handleMouseMove} on:mouseleave={handleMouseLeave} role="article">
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
        will-change: transform;
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
        clip-path: inset(0 100% 0 0);
        transition: clip-path 0.8s cubic-bezier(0.77, 0, 0.175, 1);
    }

    .project:hover .project-image,
    :global(.project.revealed) .project-image {
        clip-path: inset(0 0% 0 0);
    }

    .project-image img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        transform: scale(1.15);
        transition: transform 0.8s cubic-bezier(0.77, 0, 0.175, 1);
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
        background-color: rgba(13, 13, 15, 0.3);
        transition: background-color 0.5s ease;
    }

    .project:hover .project-image img {
        transform: scale(1.0);
    }

    .project:hover .image-overlay {
        background-color: rgba(13, 13, 15, 0);
    }
</style>