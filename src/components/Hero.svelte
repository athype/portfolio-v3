<script>
    import { onMount, onDestroy } from 'svelte';
    import { gsap } from 'gsap';

    let heroSection;
    let cloudElement;
    let windowWidth = 0;
    let windowHeight = 0;
    let isMouseMoving = false;
    let lastMouseX = 0;
    let lastMouseY = 0;
    let mouseMoveTimeout; // Store timeout reference in component scope instead of Window

    // Track mouse position with better handling to prevent snapping
    function handleMouseMove(e) {
        if (!cloudElement) return;

        lastMouseX = e.clientX;
        lastMouseY = e.clientY;

        // Get position relative to the center of the screen
        const xRatio = (lastMouseX / windowWidth - 0.5) * 2; // -1 to 1 range
        const yRatio = (lastMouseY / windowHeight - 0.5) * 2; // -1 to 1 range

        // Apply movement
        const moveX = xRatio * 90; // Using your increased range
        const moveY = yRatio * 90; // Using your increased range

        // Set flag that mouse is moving
        isMouseMoving = true;

        // Use GSAP for smoother movement with no conflicts
        gsap.killTweensOf(cloudElement, "x,y"); // Kill any competing animations
        gsap.to(cloudElement, {
            x: moveX,
            y: moveY,
            duration: 1,
            ease: "power2.out",
            overwrite: "auto" // Ensure no competing animations
        });

        // Reset the flag after movement completes
        clearTimeout(mouseMoveTimeout);
        mouseMoveTimeout = setTimeout(() => {
            isMouseMoving = false;
        }, 1200); // Slightly longer than animation duration
    }

    // Store animation references for cleanup
    let pulseAnimation;
    let floatAnimation;
    let tickerFunction;

    onMount(() => {
        console.log("Hero component mounted");
        windowWidth = window.innerWidth;
        windowHeight = window.innerHeight;

        // Handle window resize
        const handleResize = () => {
            windowWidth = window.innerWidth;
            windowHeight = window.innerHeight;
        };

        window.addEventListener('resize', handleResize);
        window.addEventListener('mousemove', handleMouseMove);

        if (cloudElement) {
            // Reset any transforms that might be affecting initial position
            gsap.set(cloudElement, {
                x: 0,
                y: 0,
                scale: 0.8,
                opacity: 0,
                transformOrigin: "center center"
            });

            // Animate cloud in
            gsap.to(cloudElement, {
                scale: 1,
                opacity: 0.9, // Using your vibrant cloud setting
                duration: 1.5,
                ease: "power2.out"
            });

            // Gentle pulsing animation for the cloud - only affects scale, not position
            pulseAnimation = gsap.to(cloudElement, {
                scale: 0.95,
                duration: 4,
                repeat: -1,
                yoyo: true,
                ease: "sine.inOut"
            });

            // Subtle floating animation - conflicts with mouse movement
            floatAnimation = gsap.to(cloudElement, {
                y: '+=15',
                duration: 5,
                repeat: -1,
                yoyo: true,
                ease: "sine.inOut",
                paused: true // Start paused
            });

            // Function to manage animations based on mouse state
            tickerFunction = () => {
                if (!isMouseMoving) {
                    if (floatAnimation.paused()) {
                        // Calculate current y position to avoid jumps
                        const currentY = gsap.getProperty(cloudElement, "y");
                        gsap.set(floatAnimation.targets()[0], { y: currentY });
                        floatAnimation.play(0);
                    }
                } else {
                    if (!floatAnimation.paused()) {
                        floatAnimation.pause();
                    }
                }
            };

            // Only apply floating animation when mouse isn't moving
            gsap.ticker.add(tickerFunction);
        }

        return () => {
            // This function is called when the component is unmounted
            window.removeEventListener('mousemove', handleMouseMove);
            window.removeEventListener('resize', handleResize);
            clearTimeout(mouseMoveTimeout);
        };
    });

    onDestroy(() => {
        // Clean up all GSAP animations
        if (pulseAnimation) pulseAnimation.kill();
        if (floatAnimation) floatAnimation.kill();
        if (tickerFunction) gsap.ticker.remove(tickerFunction);

        // Double-check timeout is cleared
        clearTimeout(mouseMoveTimeout);
    });
</script>

<section class="hero" bind:this={heroSection}>
    <!-- Dot grid background -->
    <div class="dot-grid"></div>

    <!-- Cloud element -->
    <div class="cloud-wrapper">
        <div class="cloud-element" bind:this={cloudElement}></div>
    </div>

    <!-- Content -->
    <div class="container">
        <h1>Software Developer Crafting Digital Experiences</h1>
        <p>Building performant & visually engaging applications that bring ideas to life.</p>
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
        overflow: hidden;
    }

    /* Dot grid background */
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

    /* Wrapper to center the cloud properly */
    .cloud-wrapper {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        pointer-events: none;
        z-index: 1;
    }

    /* Vibrant cloud element */
    .cloud-element {
        width: 65%;
        height: 65%;
        max-width: 900px;
        max-height: 900px;
        border-radius: 50%;
        background: radial-gradient(
                ellipse at center,
                rgba(135, 130, 255, 0.45) 0%,
                rgba(128, 124, 224, 0.3) 40%,
                rgba(111, 107, 224, 0.15) 70%,
                rgba(13, 13, 15, 0) 90%
        );
        filter: blur(40px);
        mix-blend-mode: screen;
        will-change: transform;
        pointer-events: none;
        opacity: 0;
        transform-origin: center center;
    }

    /* Content styling */
    .container {
        position: relative;
        z-index: 2; /* Ensure content is above background elements */
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
        margin-bottom: 3rem;
    }

    .hero-cta {
        display: flex;
        gap: 2rem;
        align-items: center;
        justify-content: center;
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
            min-height: 100vh;
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

        .cloud-element {
            width: 85%;
            height: 65%;
        }

        .dot-grid {
            background-size: 15px 15px;
        }
    }
</style>