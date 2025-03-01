<script>
    import { onMount, onDestroy } from 'svelte';
    import { gsap } from 'gsap';
    import Icon from './Icon.svelte';

    // Menu options
    const menuItems = [
        { id: 'home', label: 'Home', href: '#', icon: 'home' },
        { id: 'work', label: 'Work', href: '#projects', icon: 'work' },
        { id: 'about', label: 'About', href: '#about', icon: 'about' },
        { id: 'contact', label: 'Contact', href: '#contact', icon: 'contact' }
    ];

    // State variables
    let isOpen = false;
    let menuContainer;
    let centerButton;
    let radialItems = [];
    let timeline;

    // Toggle menu state
    function toggleMenu() {
        isOpen = !isOpen;

        if (isOpen) {
            openMenu();
        } else {
            closeMenu();
        }
    }

    // Open the radial menu with animation
    function openMenu() {
        if (!timeline) return;

        timeline.play();

        // Optional: add background blur when menu is open
        document.body.classList.add('radial-menu-active');
    }

    // Close the radial menu with animation
    function closeMenu() {
        if (!timeline) return;

        timeline.reverse();

        // Remove background blur
        document.body.classList.remove('radial-menu-active');
    }

    // Navigate to section
    function navigateTo(href, event) {
        event.preventDefault();
        closeMenu();

        // Small delay to allow menu closing animation
        setTimeout(() => {
            const element = document.querySelector(href);
            if (element) {
                element.scrollIntoView({ behavior: 'smooth' });
            } else if (href === '#') {
                // Scroll to top for home
                window.scrollTo({ top: 0, behavior: 'smooth' });
            }
        }, 400);
    }

    onMount(() => {
        // Initialize the GSAP animation
        timeline = gsap.timeline({ paused: true });

        if (radialItems && radialItems.length) {
            const totalItems = radialItems.length;
            const angleStep = (2 * Math.PI) / totalItems;
            const radius = 85; // Slightly smaller radius to ensure items stay on screen

            // Animate the center button
            timeline.to(centerButton, {
                rotation: 45,
                duration: 0.4,
                ease: "power2.inOut"
            }, 0);

            // Animate each menu item in a full circular pattern
            radialItems.forEach((item, index) => {
                // Start with the first item at the top (-90 degrees or -π/2 radians)
                const angle = -Math.PI/2 + (angleStep * index);
                const x = radius * Math.cos(angle);
                const y = radius * Math.sin(angle);

                timeline.to(item, {
                    x: x,
                    y: y,
                    opacity: 1,
                    scale: 1,
                    duration: 0.5,
                    ease: "back.out(1.4)",
                    delay: index * 0.05 // Stagger the animations
                }, 0);
            });

            // Add a subtle pulse to the center when open
            timeline.to(centerButton, {
                boxShadow: "0 0 20px rgba(111, 107, 224, 0.7)",
                duration: 0.4
            }, 0);
        }

        // Event handlers for closing menu when clicking outside
        const handleClickOutside = (event) => {
            if (isOpen && menuContainer && !menuContainer.contains(event.target)) {
                closeMenu();
                isOpen = false;
            }
        };

        // Add event listeners
        document.addEventListener('click', handleClickOutside);

        return () => {
            // Cleanup event listeners
            document.removeEventListener('click', handleClickOutside);

            if (timeline) {
                timeline.kill();
            }
        };
    });
</script>

<div class="radial-menu-container" bind:this={menuContainer}>
    <!-- Center toggle button -->
    <button
            class="radial-menu-toggle"
            class:active={isOpen}
            bind:this={centerButton}
            on:click={toggleMenu}
            aria-label="Toggle navigation menu"
    >
        <div class="toggle-icon">
            <span></span>
            <span></span>
        </div>
    </button>

    <!-- Radial menu items -->
    <div class="radial-menu-items">
        {#each menuItems as item, i}
            <a
                    href={item.href}
                    class="radial-menu-item"
                    bind:this={radialItems[i]}
                    on:click={(e) => navigateTo(item.href, e)}
                    aria-label={item.label}
            >
        <span class="radial-item-icon">
          <Icon name={item.icon} size="18px" color="var(--text-primary)" />
        </span>
                <span class="radial-item-label">{item.label}</span>
            </a>
        {/each}
    </div>
</div>

<style>
    .radial-menu-container {
        position: fixed;
        bottom: 7rem; /* Maintained higher position */
        right: 7rem; /* Maintained more inward position */
        z-index: 1000;
        width: 3.5rem;
        height: 3.5rem;
    }

    /* Center toggle button */
    .radial-menu-toggle {
        position: absolute;
        width: 3.5rem;
        height: 3.5rem;
        background-color: var(--accent);
        border: none;
        border-radius: 50%;
        cursor: pointer;
        z-index: 1001;
        display: flex;
        align-items: center;
        justify-content: center;
        transition: all 0.3s ease;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
    }

    .radial-menu-toggle:hover {
        transform: scale(1.05);
        background-color: rgba(111, 107, 224, 0.9);
    }

    .radial-menu-toggle.active {
        background-color: rgba(111, 107, 224, 0.8);
    }

    /* Toggle icon (plus/cross) */
    .toggle-icon {
        position: relative;
        width: 18px;
        height: 18px;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .toggle-icon span {
        position: absolute;
        width: 100%;
        height: 2px;
        background-color: white;
        transition: transform 0.3s ease;
    }

    .toggle-icon span:first-child {
        transform: rotate(90deg);
    }

    /* Active state creates an X */
    .radial-menu-toggle.active .toggle-icon span:first-child {
        transform: rotate(135deg);
    }

    .radial-menu-toggle.active .toggle-icon span:last-child {
        transform: rotate(45deg);
    }

    /* Menu items container */
    .radial-menu-items {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        pointer-events: none;
    }

    /* Individual menu items */
    .radial-menu-item {
        position: absolute;
        top: 50%;
        left: 50%;
        width: 3rem;
        height: 3rem;
        margin-top: -1.5rem;
        margin-left: -1.5rem;
        background-color: var(--bg-primary);
        border: 2px solid var(--accent);
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        transform: scale(0.7);
        opacity: 0;
        transition: background-color 0.3s ease, transform 0.2s ease;
        pointer-events: auto;
        box-shadow: 0 1px 6px rgba(0, 0, 0, 0.2);
        color: var(--text-primary);
        text-decoration: none;
    }

    .radial-menu-item:hover {
        background-color: var(--accent);
        transform: scale(1.1) translate(var(--x), var(--y)) !important;
    }

    .radial-menu-item:hover .radial-item-icon {
        transform: scale(1.2);
    }

    .radial-menu-item:hover .radial-item-label {
        opacity: 1;
        visibility: visible;
        transform: translateY(-3.5rem);
    }

    /* Icons inside menu items */
    .radial-item-icon {
        display: flex;
        align-items: center;
        justify-content: center;
        transition: transform 0.2s ease;
    }

    .radial-menu-item:hover :global(svg) {
        fill: white;
    }

    /* Labels that appear on hover */
    .radial-item-label {
        position: absolute;
        top: 0;
        background-color: var(--accent);
        color: white;
        padding: 0.3rem 0.8rem;
        border-radius: 1rem;
        font-size: 0.875rem;
        opacity: 0;
        visibility: hidden;
        transform: translateY(-3rem);
        transition: opacity 0.2s ease, transform 0.2s ease, visibility 0.2s;
        white-space: nowrap;
        font-weight: 500;
        box-shadow: 0 1px 4px rgba(0, 0, 0, 0.2);
    }

    /* Global blur effect when menu is open */
    :global(body.radial-menu-active::after) {
        content: '';
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(13, 13, 15, 0.3);
        backdrop-filter: blur(3px);
        z-index: 999;
        opacity: 0;
        pointer-events: none;
        transition: opacity 0.3s ease;
        animation: fadeIn 0.3s forwards;
    }

    @keyframes fadeIn {
        to {
            opacity: 1;
        }
    }

    /* Media queries for different screen sizes */
    @media (max-width: 768px) {
        .radial-menu-container {
            bottom: 4rem; /* Adjusted for mobile */
            right: 3rem; /* More inward than before on mobile */
        }

        .radial-menu-item:hover .radial-item-label {
            transform: translateY(-3rem);
        }
    }
</style>