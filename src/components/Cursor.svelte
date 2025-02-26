<script>
    import { onMount, onDestroy } from 'svelte';

    let cursor;
    let pointer;
    let cursorX = 0;
    let cursorY = 0;
    let pointerX = 0;
    let pointerY = 0;

    let isHovering = false;
    let isClicking = false;
    let isHidden = false;
    let isOutside = false;
    let isFirstMove = true;

    let raf;
    let mouseTimeout;

    function updateCursor() {
        const followSpeed = 0.2;

        if(!isOutside && !isHidden) {
            // For the outer cursor
            cursorX += (pointerX - cursorX) * followSpeed;
            cursorY += (pointerY - cursorY) * followSpeed;

            // Set cursor position
            cursor.style.transform = `translate3d(${cursorX}px, ${cursorY}px, 0) scale(${isHovering ? 1.5 : 1}) ${isClicking ? 'scale(0.9)' : ''}`;
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
    }

    function handleMouseDown() {
        isClicking = true;
    }

    function handleMouseUp() {
        isClicking = false;
    }

    function handleMouseEnter() {
        isOutside = false;
    }

    function handleMouseLeave() {
        isOutside = true;
        cursor.style.opacity = 0;
        pointer.style.opacity = 0;
    }

    function checkHoverElements(e) {
        const hoveredElements = document.querySelectorAll('a, button, input, textarea, [data-cursor-hover]');
        let isHovered = false;

        hoveredElements.forEach(el => {
            const rect = el.getBoundingClientRect();
            if (
                e.clientX >= rect.left &&
                e.clientX <= rect.right &&
                e.clientY >= rect.top &&
                e.clientY <= rect.bottom
            ) {
                isHovered = true;
            }
        });

        isHovering = isHovered;
    }

    onMount(() => {
        // Start the animation
        updateCursor();

        // Add event listeners
        window.addEventListener('mousemove', handleMouseMove);
        window.addEventListener('mousemove', checkHoverElements);
        window.addEventListener('mousedown', handleMouseDown);
        window.addEventListener('mouseup', handleMouseUp);
        document.addEventListener('mouseenter', handleMouseEnter);
        document.addEventListener('mouseleave', handleMouseLeave);

        // Initial state
        cursor.style.opacity = 0;
        pointer.style.opacity = 0;

        // Hide default cursor
        document.body.style.cursor = 'none';

        // Setup mouse inactivity timeout
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
        window.removeEventListener('mousemove', checkHoverElements);
        window.removeEventListener('mousedown', handleMouseDown);
        window.removeEventListener('mouseup', handleMouseUp);
        document.removeEventListener('mouseenter', handleMouseEnter);
        document.removeEventListener('mouseleave', handleMouseLeave);

        // Restore default cursor
        document.body.style.cursor = 'auto';
    });
</script>

<div class="cursor-container">
    <div class="cursor" bind:this={cursor}></div>
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
        width: 30px;
        height: 30px;
        background-color: transparent;
        border: 1px solid var(--accent);
        border-radius: 50%;
        transform-origin: center;
        transition: opacity 0.3s ease, transform 0.1s ease;
        will-change: transform;
    }

    .cursor-pointer {
        position: absolute;
        width: 8px;
        height: 8px;
        background-color: var(--accent);
        border-radius: 50%;
        transform: translate3d(0, 0, 0);
        transition: opacity 0.3s ease;
        will-change: transform;
    }

    @media (max-width: 768px) {
        .cursor, .cursor-pointer {
            display: none;
        }
    }
</style>