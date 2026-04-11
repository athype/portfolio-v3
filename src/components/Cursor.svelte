<script>
    import { onMount, onDestroy } from 'svelte';

    let cursor;
    let pointer;
    let label;
    let cursorX = 0;
    let cursorY = 0;
    let pointerX = 0;
    let pointerY = 0;

    let isHovering = false;
    let isClicking = false;
    let isHidden = false;
    let isOutside = false;
    let isFirstMove = true;
    let cursorState = ''; // '', 'link', 'project', 'action'
    let cursorLabel = '';

    let raf;
    let mouseTimeout;

    function updateCursor() {
        const followSpeed = 0.15;

        if(!isOutside && !isHidden) {
            cursorX += (pointerX - cursorX) * followSpeed;
            cursorY += (pointerY - cursorY) * followSpeed;

            cursor.style.transform = `translate3d(${cursorX}px, ${cursorY}px, 0)`;
            pointer.style.transform = `translate3d(${pointerX}px, ${pointerY}px, 0)`;

            cursor.style.opacity = isFirstMove ? 0 : 1;
            pointer.style.opacity = isFirstMove ? 0 : 1;
        }

        raf = requestAnimationFrame(updateCursor);
    }

    function handleMouseMove(e) {
        pointerX = e.clientX;
        pointerY = e.clientY;

        if (isFirstMove) {
            cursorX = e.clientX;
            cursorY = e.clientY;
            isFirstMove = false;
        }

        if(isHidden) {
            isHidden = false;
            cursor.style.opacity = 1;
            pointer.style.opacity = 1;
        }

        clearTimeout(mouseTimeout);
        mouseTimeout = setTimeout(() => {
            isHidden = true;
            cursor.style.opacity = 0;
            pointer.style.opacity = 0;
        }, 5000);

        // Detect what we're hovering
        const target = /** @type {HTMLElement} */ (e.target);
        const closestLink = target.closest('a');
        const closestButton = target.closest('button, .btn, .button, .copy-btn');
        const closestProject = target.closest('.project');

        if (closestProject) {
            cursorState = 'project';
            cursorLabel = 'View';
            isHovering = true;
        } else if (closestLink) {
            cursorState = 'link';
            cursorLabel = '';
            isHovering = true;
        } else if (closestButton) {
            cursorState = 'action';
            cursorLabel = '';
            isHovering = true;
        } else {
            cursorState = '';
            cursorLabel = '';
            isHovering = false;
        }

        // Apply state classes (use classList to preserve Svelte's scoped class)
        cursor.classList.remove('link', 'project', 'action');
        if (cursorState) {
            cursor.classList.add(cursorState);
        }
        label.textContent = cursorLabel;
    }

    function handleMouseDown() {
        isClicking = true;
        cursor.classList.add('clicking');
    }

    function handleMouseUp() {
        isClicking = false;
        cursor.classList.remove('clicking');
    }

    function handleMouseEnter() {
        isOutside = false;
    }

    function handleMouseLeave() {
        isOutside = true;
        cursor.style.opacity = 0;
        pointer.style.opacity = 0;
    }

    onMount(() => {
        updateCursor();

        window.addEventListener('mousemove', handleMouseMove);
        window.addEventListener('mousedown', handleMouseDown);
        window.addEventListener('mouseup', handleMouseUp);
        document.addEventListener('mouseenter', handleMouseEnter);
        document.addEventListener('mouseleave', handleMouseLeave);

        cursor.style.opacity = 0;
        pointer.style.opacity = 0;

        document.body.style.cursor = 'none';

        mouseTimeout = setTimeout(() => {
            isHidden = true;
            cursor.style.opacity = 0;
            pointer.style.opacity = 0;
        }, 5000);
    });

    onDestroy(() => {
        cancelAnimationFrame(raf);
        clearTimeout(mouseTimeout);

        window.removeEventListener('mousemove', handleMouseMove);
        window.removeEventListener('mousedown', handleMouseDown);
        window.removeEventListener('mouseup', handleMouseUp);
        document.removeEventListener('mouseenter', handleMouseEnter);
        document.removeEventListener('mouseleave', handleMouseLeave);

        document.body.style.cursor = 'auto';
    });
</script>

<div class="cursor-container">
    <div class="cursor" bind:this={cursor}>
        <span class="cursor-label" bind:this={label}></span>
    </div>
    <div class="cursor-pointer" bind:this={pointer}></div>
</div>

<style>
    .cursor-container {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        pointer-events: none;
        z-index: 9999;
    }

    .cursor {
        position: absolute;
        top: -15px;
        left: -15px;
        width: 30px;
        height: 30px;
        background-color: transparent;
        border: 1px solid var(--accent);
        border-radius: 50%;
        transform-origin: center;
        transition: width 0.4s cubic-bezier(0.23, 1, 0.32, 1),
                    height 0.4s cubic-bezier(0.23, 1, 0.32, 1),
                    top 0.4s cubic-bezier(0.23, 1, 0.32, 1),
                    left 0.4s cubic-bezier(0.23, 1, 0.32, 1),
                    background-color 0.3s ease,
                    border-color 0.3s ease,
                    opacity 0.3s ease;
        will-change: transform;
        display: flex;
        align-items: center;
        justify-content: center;
        mix-blend-mode: difference;
    }

    .cursor-label {
        font-size: 0;
        font-weight: 600;
        color: var(--bg-primary);
        letter-spacing: 0.05em;
        text-transform: uppercase;
        transition: font-size 0.3s cubic-bezier(0.23, 1, 0.32, 1);
        white-space: nowrap;
    }

    /* Link hover — expand ring */
    :global(.cursor.link) {
        width: 50px;
        height: 50px;
        top: -25px;
        left: -25px;
        border-color: var(--accent);
        background-color: rgba(255, 197, 61, 0.08);
    }

    /* Button hover — filled accent */
    :global(.cursor.action) {
        width: 50px;
        height: 50px;
        top: -25px;
        left: -25px;
        background-color: var(--accent);
        border-color: var(--accent);
    }

    /* Project hover — large circle with label */
    :global(.cursor.project) {
        width: 80px;
        height: 80px;
        top: -40px;
        left: -40px;
        background-color: var(--accent);
        border-color: var(--accent);
        mix-blend-mode: normal;
    }

    :global(.cursor.project) .cursor-label {
        font-size: 0.7rem;
        color: var(--bg-primary);
    }

    /* Click state */
    :global(.cursor.clicking) {
        transform: scale(0.85);
    }

    .cursor-pointer {
        position: absolute;
        top: -3px;
        left: -3px;
        width: 6px;
        height: 6px;
        background-color: var(--accent);
        border-radius: 50%;
        transition: opacity 0.3s ease;
        will-change: transform;
    }

    @media (max-width: 768px) {
        .cursor, .cursor-pointer {
            display: none;
        }
    }
</style>