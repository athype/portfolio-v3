<script>
    import { onMount } from 'svelte';
    import { gsap } from 'gsap';

    let preloader;
    let counter;
    let nameText;
    let count = 0;

    export let onComplete = () => {};

    onMount(() => {
        const tl = gsap.timeline({
            onComplete: () => {
                // Reveal the page
                gsap.to(preloader, {
                    yPercent: -100,
                    duration: 0.8,
                    ease: 'power4.inOut',
                    onComplete
                });
            }
        });

        // Counter animation
        const obj = { val: 0 };
        tl.to(obj, {
            val: 100,
            duration: 2,
            ease: 'power2.inOut',
            onUpdate: () => {
                count = Math.round(obj.val);
                if (counter) counter.textContent = count + '%';
            }
        });

        // Name reveal
        tl.from(nameText, {
            y: 60,
            opacity: 0,
            duration: 0.6,
            ease: 'power3.out'
        }, 0.3);

        // Fade out name + counter before curtain pull
        tl.to([nameText, counter], {
            opacity: 0,
            y: -30,
            duration: 0.4,
            ease: 'power2.in'
        }, '-=0.3');
    });
</script>

<div class="preloader" bind:this={preloader}>
    <div class="preloader-content">
        <div class="preloader-name" bind:this={nameText}>Krisztian Kozari</div>
        <div class="preloader-counter" bind:this={counter}>0%</div>
    </div>
</div>

<style>
    .preloader {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: var(--bg-primary);
        z-index: 9999;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .preloader-content {
        text-align: center;
    }

    .preloader-name {
        font-size: 2.5rem;
        font-weight: 700;
        letter-spacing: 0.05em;
        color: var(--text-primary);
        margin-bottom: 1.5rem;
    }

    .preloader-counter {
        font-size: 5rem;
        font-weight: 300;
        color: var(--accent);
        font-variant-numeric: tabular-nums;
    }

    @media (max-width: 768px) {
        .preloader-name {
            font-size: 1.5rem;
        }
        .preloader-counter {
            font-size: 3rem;
        }
    }
</style>
