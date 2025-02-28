<script>
    const currentYear = new Date().getFullYear();
</script>

<footer>
    <div class="container">
        <div class="footer-content">
            <div class="footer-logo">
                <a href="/" class="logo">Krisztian Kozari</a>
                <p>Software Developer crafting digital experiences that make an impact.</p>
            </div>

            <div class="footer-nav">
                <h3>Quick Links</h3>
                <ul>
                    <li><a href="#about">About</a></li>
                    <li><a href="#projects">Projects</a></li>
                    <li><a href="#contact">Contact</a></li>
                </ul>
            </div>

            <div class="footer-social">
                <h3>Connect</h3>
                <div class="social-links">
                    <a href="https://github.com/athype" target="_blank" rel="noopener noreferrer">GitHub</a>
                    <a href="https://www.linkedin.com/in/krisztian-kozari/" target="_blank" rel="noopener noreferrer">LinkedIn</a>
                </div>
            </div>
        </div>

        <div class="footer-bottom">
            <p>&copy; {currentYear} Krisztian Kozari. All rights reserved.</p>
            <button class="scroll-top" on:click={() => window.scrollTo({ top: 0, behavior: 'smooth' })}>
                Back to top ↑
            </button>
        </div>
    </div>
</footer>

<style>
    footer {
        padding: 5rem 0 2rem;
        background-color: var(--bg-secondary);
        border-top: 1px solid var(--border-color);
    }

    .footer-content {
        display: grid;
        grid-template-columns: 2fr 1fr 1fr;
        gap: 3rem;
        margin-bottom: 4rem;
    }

    .logo {
        font-size: 1.2rem;
        font-weight: 700;
        letter-spacing: 0.1rem;
        margin-bottom: 1rem;
        display: inline-block;
    }

    .footer-logo p {
        color: var(--text-secondary);
        max-width: 300px;
        line-height: 1.6;
    }

    h3 {
        font-size: 1rem;
        margin-bottom: 1.5rem;
        color: var(--accent);
    }

    .footer-nav ul {
        list-style: none;
    }

    .footer-nav li {
        margin-bottom: 0.8rem;
    }

    .footer-nav a,
    .social-links a {
        color: var(--text-secondary);
        transition: color 0.2s ease;
    }

    .footer-nav a:hover,
    .social-links a:hover {
        color: var(--accent);
    }

    .social-links {
        display: flex;
        flex-direction: column;
        gap: 0.8rem;
    }

    .footer-bottom {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding-top: 2rem;
        border-top: 1px solid var(--border-color);
        color: var(--text-secondary);
        font-size: 0.9rem;
    }

    .scroll-top {
        background: transparent;
        color: var(--text-secondary);
        border: none;
        cursor: pointer;
        font-size: 0.9rem;
        padding: 0;
        transition: color 0.2s ease;
    }

    .scroll-top:hover {
        color: var(--accent);
        background: transparent;
    }

    @media (max-width: 768px) {
        .footer-content {
            grid-template-columns: 1fr;
            gap: 2rem;
        }

        .footer-bottom {
            flex-direction: column;
            gap: 1rem;
            align-items: flex-start;
        }
    }
</style>