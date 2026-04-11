<script>
    import { onMount } from 'svelte';
    import { fade, fly } from 'svelte/transition';
    import { gsap } from 'gsap';
    import { ScrollTrigger } from 'gsap/ScrollTrigger';
    import WireframeGlobe from './WireframeGlobe.svelte';

    let heroRef;
    let dotGridRef;
    let globeRef;
    let contentRef;

    onMount(() => {
        gsap.registerPlugin(ScrollTrigger);

        // Parallax: dot grid moves slower (background layer)
        gsap.to(dotGridRef, {
            yPercent: -30,
            ease: 'none',
            scrollTrigger: {
                trigger: heroRef,
                start: 'top top',
                end: 'bottom top',
                scrub: true
            }
        });

        // Parallax: globe moves at medium speed
        gsap.to(globeRef, {
            yPercent: -50,
            ease: 'none',
            scrollTrigger: {
                trigger: heroRef,
                start: 'top top',
                end: 'bottom top',
                scrub: true
            }
        });

        // Parallax: text content moves fastest (foreground)
        gsap.to(contentRef, {
            yPercent: -80,
            opacity: 0,
            ease: 'none',
            scrollTrigger: {
                trigger: heroRef,
                start: 'top top',
                end: 'bottom top',
                scrub: true
            }
        });
    });
</script>

<section class="hero" bind:this={heroRef}>
    <div class="dot-grid" bind:this={dotGridRef}></div>
    <div class="content" bind:this={contentRef}>
        <div class="intro" in:fade={{ duration: 800, delay: 300 }}>
            <h1>Hi, I'm <span class="name">Krisztian</span></h1>
        </div>

        <div class="globe-container" bind:this={globeRef} in:fade={{ duration: 1200, delay: 500 }}>
            <WireframeGlobe />
        </div>

        <div class="tagline" in:fly={{ y: 20, duration: 800, delay: 800 }}>
            <h2>Building digital worlds from backend to interface</h2>
            <div class="cta">
                <a href="#projects" class="btn">View My Work</a>
                <a href="#contact" class="btn btn-outline">Get In Touch</a>
            </div>
        </div>
    </div>
</section>

<style>
    .dot-grid {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 0;
        background-image: radial-gradient(
                rgba(248, 246, 246, 0.15) 1px,
                transparent 1px
        );
        background-size: 25px 25px;
        opacity: 1;
        pointer-events: none;
    }

    .hero {
        min-height: 100vh;
        display: flex;
        align-items: center;
        justify-content: center;
        position: relative;
        overflow: hidden;
        padding: 0 1rem;
    }

    .content {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        z-index: 1;
        width: 100%;
        max-width: 1200px;
        text-align: center;
    }

    .intro h1 {
        font-size: 2.5rem;
        margin-bottom: 1rem;
        font-weight: 400;
    }

    .name {
        color: var(--accent);
        font-weight: 700;
    }

    .globe-container {
        height: 280px;
        width: 280px;
        margin: 2rem 0;
        position: relative;
    }

    .tagline {
        margin-top: 1rem;
    }

    .tagline h2 {
        font-size: 2.2rem;
        line-height: 1.3;
        max-width: 500px;
        margin: 0 auto 2rem;
        background: linear-gradient(to right, var(--accent), var(--rose));
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        background-clip: text;
    }

    .cta {
        display: flex;
        gap: 1rem;
        justify-content: center;
        margin-top: 2rem;
    }

    .btn {
        padding: 0.8rem 1.5rem;
        border-radius: 4px;
        font-weight: 600;
        letter-spacing: 0.5px;
        text-decoration: none;
        transition: all 0.3s ease;
        font-size: 1rem;
    }

    .btn:first-child {
        background: var(--accent);
        color: #0a192f;
    }

    .btn:first-child:hover {
        transform: translateY(-5px);
        box-shadow: 0 10px 20px var(--accent-hover);
    }

    .btn-outline {
        border: 2px solid var(--accent);
        color: var(--accent);
        background: transparent;
    }

    .btn-outline:hover {
        background: var(--accent-hover);
        transform: translateY(-5px);
    }

    @media (max-width: 768px) {
        .intro h1 {
            font-size: 2rem;
        }

        .globe-container {
            height: 220px;
            width: 220px;
            margin: 1rem 0;
        }

        .tagline h2 {
            font-size: 1.8rem;
        }

        .cta {
            flex-direction: column;
            align-items: center;
        }
    }
</style>