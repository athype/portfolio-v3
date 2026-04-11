<script>
    import { onMount } from 'svelte';
    import { gsap } from 'gsap';
    import { ScrollTrigger } from 'gsap/ScrollTrigger';
    import ProjectCard from './ProjectCard.svelte';

    const projects = [
        {
            title: 'Drink administration platform',
            description: 'A platform for managing and tracking drink consumption, built with Vue and Node.js.',
            tags: ['Vue', 'Node.js', 'ExpressJs', 'PostgreSQL', 'Docker', 'Webauthn'],
            image: '/beermachine.png',
            link: 'https://beer.sv-ada.nl'
        },
        {
            title: 'Alchemix',
            description: 'A hybrid app for cocktail recipes and inventory keeping, built with React Native and NestJS.',
            tags: ['React Native', 'NestJS', 'PostgreSQL', 'Docker', 'Coolify'],
            image: '/alchemix.png',
            link: 'https://alchemix.krisztiankozari.com/'
        },
        {
            title: 'Zenclash.ing',
            description: 'A clash of clans clan family landing page and war line-up management webapp.',
            tags: ['Vue', 'Node.js', 'ExpressJs', 'Docker', 'Discord oAuth', 'Clash of Clans API', 'Clash King API'],
            link: 'https://zenclash.ing'
        },
        {
            title: 'Basecrusher.com',
            description: 'A clash of clans army composition and guide website with a focus on user experience and performance.',
            image: '/basecrusher.png',
            tags: ['Svelte', 'Node.js', 'ExpressJs', 'SQLite'],
            link: 'https://basecrusher.com'
        },
        {
            title: 'Scorion Feedback system',
            description: 'A proof of concept feedback system for Scorion using OpenAI Whisper. Developed as a group project',
            image: '/feedback.png',
            tags: ['Svelte', 'ExpressJs', 'OpenAI Whisper', "SQLite", "Node.js"],
            link: '#'
        }
    ];

    let sectionRef;
    let trackRef;

    onMount(() => {
        gsap.registerPlugin(ScrollTrigger);

        // Wait for layout
        setTimeout(() => {
            const totalScroll = trackRef.scrollWidth - window.innerWidth;

            gsap.to(trackRef, {
                x: -totalScroll,
                ease: 'none',
                scrollTrigger: {
                    trigger: sectionRef,
                    pin: true,
                    scrub: 1,
                    end: () => `+=${totalScroll}`,
                    invalidateOnRefresh: true,
                }
            });
        }, 600);
    });
</script>

<section id="projects" class="section projects-section" bind:this={sectionRef}>
    <div class="projects-header container">
        <h2 class="split-text">Selected Projects</h2>
    </div>

    <div class="horizontal-track" bind:this={trackRef}>
        {#each projects as project, i}
            <div class="horizontal-card">
                <ProjectCard {...project} index={i} />
            </div>
        {/each}

        <div class="horizontal-card end-card">
            <div class="more-projects">
                <p>Interested in seeing more?</p>
                <a href="https://github.com/athype" target="_blank" rel="noopener noreferrer" class="button">
                    View GitHub Profile
                </a>
            </div>
        </div>
    </div>
</section>

<style>
    h2 {
        font-size: 3rem;
        margin-bottom: 2rem;
    }

    .projects-section {
        overflow: hidden;
        padding-bottom: 0 !important;
    }

    .projects-header {
        margin-bottom: 2rem;
    }

    .horizontal-track {
        display: flex;
        gap: 4rem;
        padding: 0 var(--spacing-md);
        will-change: transform;
    }

    .horizontal-card {
        flex-shrink: 0;
        width: 80vw;
        max-width: 900px;
    }

    .end-card {
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .more-projects {
        text-align: center;
    }

    .more-projects p {
        font-size: 1.5rem;
        margin-bottom: 2rem;
        color: var(--text-secondary);
    }

    .button {
        color: #0a192f;
    }

    @media (max-width: 768px) {
        .horizontal-card {
            width: 85vw;
        }
    }
</style>