<script>
    import { onMount } from 'svelte';

    $: isScrolled = false;
    $: menuOpen = false;

    onMount(() => {
        const handleScroll = () => {
            isScrolled = window.scrollY > 50;
        };
        window.addEventListener('scroll', handleScroll);
        return () => window.removeEventListener('scroll', handleScroll);
    });

    function toggleMenu() {
        menuOpen = !menuOpen;
        if(menuOpen) {
            document.body.style.overflow = 'hidden';
        } else {
            document.body.style.overflow = '';
        }
    }

    function closeMenu() {
        menuOpen = false;
        document.body.style.overflow = '';
    }
</script>

<header class:scrolled={isScrolled}>
    <div class="container header-container">
        <a href="/" class="logo">ATHYPE</a>

        <button class="menu-toggle" on:click={toggleMenu} aria-label="Toggle menu">
            <div class="hamburger" class:active={menuOpen}>
                <span></span>
                <span></span>
            </div>
        </button>

        <nav class:active={menuOpen}>
            <ul>
                <li><a href="#about" on:click={closeMenu}>About</a></li>
                <li><a href="#projects" on:click={closeMenu}>Projects</a></li>
                <li><a href="#contact" on:click={closeMenu}>Contact</a></li>
            </ul>
        </nav>
    </div>
</header>

<style>
    header {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        z-index: 100;
        padding: 1.5rem 0;
        transition: all 0.3s ease;
    }

    header.scrolled {
        background-color: rgba(13, 13, 15, 0.9);
        backdrop-filter: blur(10px);
        padding: 1rem 0;
        border-bottom: 1px solid var(--border-color);
    }

    .header-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .logo {
        font-size: 1.2rem;
        font-weight: 700;
        letter-spacing: 0.1rem;
    }

    nav ul {
        display: flex;
        list-style: none;
        gap: 2rem;
    }

    nav a {
        position: relative;
        font-weight: 500;
        transition: color 0.2s ease;
    }

    nav a::after {
        content: '';
        position: absolute;
        width: 0;
        height: 2px;
        bottom: -4px;
        left: 0;
        background-color: var(--accent);
        transition: width 0.3s ease;
    }

    nav a:hover::after {
        width: 100%;
    }

    .menu-toggle {
        display: none;
        background: transparent;
        border: none;
        padding: 0;
        cursor: pointer;
        z-index: 110;
    }

    .hamburger {
        position: relative;
        width: 24px;
        height: 24px;
    }

    .hamburger span {
        position: absolute;
        width: 100%;
        height: 2px;
        background-color: var(--text-primary);
        transition: transform 0.3s ease;
    }

    .hamburger span:first-child {
        top: 8px;
    }

    .hamburger span:last-child {
        top: 16px;
    }

    .hamburger.active span:first-child {
        transform: rotate(45deg) translate(2px, 6px);
    }

    .hamburger.active span:last-child {
        transform: rotate(-45deg) translate(1px, -5px);
    }

    @media (max-width: 768px) {
        .menu-toggle {
            display: block;
        }

        nav {
            position: fixed;
            top: 0;
            right: 0;
            width: 100%;
            height: 100vh;
            background-color: var(--bg-primary);
            display: flex;
            align-items: center;
            justify-content: center;
            transform: translateX(100%);
            transition: transform 0.5s cubic-bezier(0.77, 0.2, 0.05, 1.0);
        }

        nav.active {
            transform: translateX(0);
        }

        nav ul {
            flex-direction: column;
            align-items: center;
            gap: 3rem;
        }

        nav a {
            font-size: 1.5rem;
        }
    }
</style>