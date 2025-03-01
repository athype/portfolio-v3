<script>
    import { onMount, onDestroy } from 'svelte';
    import { gsap } from 'gsap';
    import Icon from './Icon.svelte';

    // State variables
    let isOpen = false;
    let currentlyActive = 'home'; // Default to home section
    let menuContainer;
    let circleBorder;
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
    }

    // Close the radial menu with animation
    function closeMenu() {
        if (!timeline) return;
        timeline.reverse();
    }

    // Navigate to section and update active state
    function navigateTo(id, href, event) {
        event.preventDefault();
        currentlyActive = id;

        // Navigate to the section
        setTimeout(() => {
            if (href === '#') {
                // Scroll to top for home
                window.scrollTo({ top: 0, behavior: 'smooth' });
            } else {
                const element = document.querySelector(href);
                if (element) {
                    element.scrollIntoView({ behavior: 'smooth' });
                }
            }
        }, 100);
    }

    // Simple throttle function implementation
    function throttle(fn, delay) {
        let lastCall = 0;
        return function(...args) {
            const now = Date.now();
            if (now - lastCall >= delay) {
                lastCall = now;
                return fn(...args);
            }
        };
    }

    // Watch for scroll to update active section
    function updateActiveSection() {
        const scrollPosition = window.scrollY;
        const windowHeight = window.innerHeight;

        // Get sections and their positions
        const homeSection = document.querySelector('main');
        const aboutSection = document.querySelector('#about');
        const projectsSection = document.querySelector('#projects');
        const contactSection = document.querySelector('#contact');

        // Get positions safely
        const homeTop = homeSection ? homeSection.getBoundingClientRect().top + window.scrollY : 0;
        const aboutTop = aboutSection ? aboutSection.getBoundingClientRect().top + window.scrollY : Infinity;
        const projectsTop = projectsSection ? projectsSection.getBoundingClientRect().top + window.scrollY : Infinity;
        const contactTop = contactSection ? contactSection.getBoundingClientRect().top + window.scrollY : Infinity;

        // Determine which section is currently in view
        if (scrollPosition < aboutTop - windowHeight * 0.3) {
            currentlyActive = 'home';
        } else if (scrollPosition < projectsTop - windowHeight * 0.3) {
            currentlyActive = 'about';
        } else if (scrollPosition < contactTop - windowHeight * 0.3) {
            currentlyActive = 'work';
        } else {
            currentlyActive = 'contact';
        }
    }

    onMount(() => {
        // Initialize animation timeline
        timeline = gsap.timeline({ paused: true });

        // Animation for opening/closing the menu
        timeline.to(circleBorder, {
            scale: 1,
            opacity: 1,
            duration: 0.5,
            ease: "back.out(1.4)"
        }, 0);

        // Animation for menu items
        gsap.set('.menu-item', { opacity: 0, y: 10 });
        timeline.to('.menu-item', {
            opacity: 1,
            y: 0,
            stagger: 0.05,
            duration: 0.4,
            ease: "power2.out"
        }, 0.1);

        // Set up scroll listener to update active section with custom throttle
        const throttledUpdateActiveSection = throttle(updateActiveSection, 100);
        window.addEventListener('scroll', throttledUpdateActiveSection);

        // Initial check for active section
        updateActiveSection();

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
            window.removeEventListener('scroll', throttledUpdateActiveSection);

            if (timeline) {
                timeline.kill();
            }
        };
    });
</script>

<div class="radial-menu-container" bind:this={menuContainer}>
    <!-- Toggle button remains unchanged -->
    <button
            class="radial-menu-toggle"
            class:active={isOpen}
            on:click={toggleMenu}
            aria-label="Toggle navigation menu"
    >
        <div class="toggle-icon">
            <span></span>
            <span></span>
            <span></span>
        </div>
    </button>

    <!-- Circle outline -->
    <div class="circle-border" class:visible={isOpen} bind:this={circleBorder}>
        <!-- Menu items at the 4 cardinal points - using just labels now -->
        <div class="menu-item-container top" class:active={currentlyActive === 'home'}>
            <a
                    href="#"
                    class="menu-item"
                    on:click={(e) => navigateTo('home', '#', e)}
                    aria-label="Home"
            >
                <span class="label">Home</span>
            </a>
        </div>

        <div class="menu-item-container right" class:active={currentlyActive === 'work'}>
            <a
                    href="#projects"
                    class="menu-item"
                    on:click={(e) => navigateTo('work', '#projects', e)}
                    aria-label="Work"
            >
                <span class="label">Work</span>
            </a>
        </div>

        <div class="menu-item-container bottom" class:active={currentlyActive === 'contact'}>
            <a
                    href="#contact"
                    class="menu-item"
                    on:click={(e) => navigateTo('contact', '#contact', e)}
                    aria-label="Contact"
            >
                <span class="label">Contact</span>
            </a>
        </div>

        <div class="menu-item-container left" class:active={currentlyActive === 'about'}>
            <a
                    href="#about"
                    class="menu-item"
                    on:click={(e) => navigateTo('about', '#about', e)}
                    aria-label="About"
            >
                <span class="label">About</span>
            </a>
        </div>
    </div>
</div>

<style>
    .radial-menu-container {
        position: fixed;
        bottom: 5rem;
        right: 5rem;
        z-index: 1000;
        width: 12rem;
        height: 12rem;
        pointer-events: none;
    }

    /* Toggle button - perfectly circular */
    .radial-menu-toggle {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 3rem;
        height: 3rem;
        background-color: var(--bg-primary);
        border: 2px solid var(--accent);
        border-radius: 50%;
        cursor: pointer;
        z-index: 1001;
        display: flex;
        align-items: center;
        justify-content: center;
        transition: all 0.3s ease;
        pointer-events: auto;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
        padding: 0;
    }

    .toggle-icon {
        width: 18px;
        height: 18px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
    }

    .toggle-icon span {
        display: block;
        width: 18px;
        height: 2px;
        background-color: var(--accent);
        transition: transform 0.3s ease, opacity 0.2s ease;
    }

    .radial-menu-toggle.active {
        background-color: var(--accent);
    }

    .radial-menu-toggle.active .toggle-icon span {
        background-color: white;
    }

    .radial-menu-toggle.active .toggle-icon span:nth-child(1) {
        transform: translateY(8px) rotate(45deg);
    }

    .radial-menu-toggle.active .toggle-icon span:nth-child(2) {
        opacity: 0;
    }

    .radial-menu-toggle.active .toggle-icon span:nth-child(3) {
        transform: translateY(-8px) rotate(-45deg);
    }

    /* Circle border */
    .circle-border {
        position: absolute;
        top: 50%;
        left: 50%;
        width: 11rem;
        height: 11rem;
        border: 2px solid var(--accent);
        border-radius: 50%;
        transform: translate(-50%, -50%) scale(0.8);
        opacity: 0;
        transition: transform 0.3s ease, opacity 0.3s ease;
        pointer-events: none;
    }

    .circle-border.visible {
        opacity: 1;
        transform: translate(-50%, -50%) scale(1);
        pointer-events: auto;
    }

    /* Menu item containers positioning */
    .menu-item-container {
        position: absolute;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .menu-item-container.top {
        top: -35%;
        left: 50%;
        transform: translateX(-50%);
    }

    .menu-item-container.right {
        top: 50%;
        right: -45%;
        transform: translateY(-50%);
    }

    .menu-item-container.bottom {
        bottom: -35%;
        left: 50%;
        transform: translateX(-50%);
    }

    .menu-item-container.left {
        top: 50%;
        left: -45%;
        transform: translateY(-50%);
    }

    /* Menu items styling */
    .menu-item {
        display: flex;
        align-items: center;
        justify-content: center;
        text-decoration: none;
        color: var(--text-primary);
        transition: all 0.3s ease;
        padding: 0.5rem;
        border-radius: 1rem;
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
    }

    .label {
        font-size: 0.9rem;
        font-weight: 500;
        white-space: nowrap;
        padding: 0.2rem 0.5rem;
    }


    .menu-item-container.active .label {
        color: white;
        font-weight: 600;
    }

    /* Menu item hover effects */
    .menu-item:hover {
        background-color: rgba(var(--accent-rgb), 0.15);
        transform: scale(1.05);
    }

    /* Media queries for smaller screens */
    @media (max-width: 768px) {
        .radial-menu-container {
            display: none;
        }
    }

    </style>