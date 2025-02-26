<script>
    import { onMount } from 'svelte';
    import { gsap } from 'gsap';
    import SplitType from 'split-type';

    onMount(() => {
        // Split text for animation
        const heroTitle = new SplitType('#hero-title', { types: 'words,chars' });
        const heroSubtitle = new SplitType('#hero-subtitle', { types: 'words' });

        // Create animation timeline
        const tl = gsap.timeline({ defaults: { ease: 'power3.out' } });

        tl.from(heroTitle.chars, {
            opacity: 0,
            y: 20,
            duration: 0.8,
            stagger: 0.02,
            delay: 0.5
        })
            .from(heroSubtitle.words, {
                opacity: 0,
                y: 20,
                duration: 0.8,
                stagger: 0.04
            }, '-=0.2')
            .from('.hero-cta', {
                opacity: 0,
                y: 20,
                duration: 0.8
            }, '-=0.3');
    });
</script>

<section class="hero">
    <div class="container">
        <h1 id="hero-title">Software Developer Crafting Digital Experiences</h1>
        <p id="hero-subtitle">Building performant & visually engaging applications that bring ideas to life.</p>
        <div class="hero-cta">
            <a href="#projects" class="button">View my work</a>
            <a href="#about" class="text-link">Learn about me</a>
        </div>
    </div>
</section>

<style>
    .hero {
        height: 100vh;
        display: flex;
        align-items: center;
        position: relative;
        padding-top: 5rem;
    }

    h1 {
        font-size: 4rem;
        line-height: 1.1;
        max-width: 900px;
        margin-bottom: 2rem;
    }

    p {
        font-size: 1.5rem;
        color: var(--text-secondary);
        max-width: 650px;
        margin-bottom: 3rem;
    }

    .hero-cta {
        display: flex;
        gap: 2rem;
        align-items: center;
    }

    .text-link {
        position: relative;
        font-weight: 500;
        transition: color 0.2s ease;
        padding-bottom: 0.25rem;
        border-bottom: 2px solid var(--accent);
    }

    .text-link:hover {
        color: var(--accent);
    }

    @media (max-width: 768px) {
        .hero {
            height: auto;
            padding: 8rem 0 4rem;
        }

        h1 {
            font-size: 2.5rem;
        }

        p {
            font-size: 1.2rem;
        }

        .hero-cta {
            flex-direction: column;
            align-items: flex-start;
            gap: 1rem;
        }
    }
</style>