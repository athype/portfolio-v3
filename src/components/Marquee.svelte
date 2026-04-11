<script>
    export let items = [
        'Frontend', 'Backend', 'Svelte', 'Vue', 'React Native',
        'Node.js', 'Docker', 'AWS', 'TypeScript', 'NestJS',
        'PostgreSQL', 'Figma', 'Git', 'Azure', 'ExpressJs'
    ];
    export let speed = 30; // seconds per full cycle
    export let separator = '✦';
    export let direction = 'left'; // 'left' or 'right'
</script>

<div class="marquee" class:reverse={direction === 'right'}>
    <div class="marquee-track" style="--speed: {speed}s">
        {#each [0, 1] as _}
            <div class="marquee-content" aria-hidden={_ === 1}>
                {#each items as item}
                    <span class="marquee-item">{item}</span>
                    <span class="marquee-separator">{separator}</span>
                {/each}
            </div>
        {/each}
    </div>
</div>

<style>
    .marquee {
        overflow: hidden;
        padding: 1.5rem 0;
        border-top: 1px solid var(--border-color);
        border-bottom: 1px solid var(--border-color);
        position: relative;
    }

    .marquee::before,
    .marquee::after {
        content: '';
        position: absolute;
        top: 0;
        width: 100px;
        height: 100%;
        z-index: 2;
        pointer-events: none;
    }

    .marquee::before {
        left: 0;
        background: linear-gradient(to right, var(--bg-primary), transparent);
    }

    .marquee::after {
        right: 0;
        background: linear-gradient(to left, var(--bg-primary), transparent);
    }

    .marquee-track {
        display: flex;
        width: max-content;
        animation: scroll var(--speed) linear infinite;
    }

    .reverse .marquee-track {
        animation-direction: reverse;
    }

    .marquee-content {
        display: flex;
        align-items: center;
        white-space: nowrap;
    }

    .marquee-item {
        font-size: 1.4rem;
        font-weight: 600;
        text-transform: uppercase;
        letter-spacing: 0.1em;
        color: var(--text-secondary);
        padding: 0 1.5rem;
        transition: color 0.3s ease;
    }

    .marquee-item:hover {
        color: var(--accent);
    }

    .marquee-separator {
        color: var(--accent);
        font-size: 0.8rem;
        opacity: 0.6;
    }

    @keyframes scroll {
        from {
            transform: translateX(0);
        }
        to {
            transform: translateX(-50%);
        }
    }

    @media (max-width: 768px) {
        .marquee-item {
            font-size: 1rem;
            padding: 0 1rem;
        }
    }
</style>
