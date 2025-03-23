<script>
    let name = '';
    let email = '';
    let message = '';
    let isSubmitting = false;
    let submitStatus = null;

    function handleSubmit() {
        isSubmitting = true;

        // Simulate form submission
        setTimeout(() => {
            isSubmitting = false;
            submitStatus = 'success';
            name = '';
            email = '';
            message = '';

            // Reset status after 5 seconds
            setTimeout(() => {
                submitStatus = null;
            }, 5000);
        }, 1500);
    }
</script>

<section id="contact" class="section">
    <div class="container">
        <h2 class="split-text">Get In Touch</h2>

        <div class="contact-content">
            <div class="contact-info fade-in">
                <p>I'm always open to discussing new projects, creative ideas, or opportunities to be part of your vision.</p>

                <div class="contact-methods">
                    <div class="contact-method">
                        <h3>Email</h3>
                        <a href="mailto:contact@krisztiankozari.dev">contact@krisztiankozari.dev</a>
                        <button class="copy-btn" on:click={() => {
                    navigator.clipboard.writeText('contact@krisztiankozari.dev');
                    alert('Email copied to clipboard!');
                }}>Copy email</button>
                    </div>

                    <div class="contact-method">
                        <h3>Follow Me</h3>
                        <div class="social-links">
                            <a href="https://github.com/athype" target="_blank" rel="noopener noreferrer">GitHub</a>
                            <a href="https://www.linkedin.com/in/krisztian-kozari/" target="_blank" rel="noopener noreferrer">LinkedIn</a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</section>

<style>
    h2 {
        font-size: 3rem;
        margin-bottom: 3rem;
    }

    .contact-content {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 4rem;
    }

    .contact-info p {
        font-size: 1.1rem;
        line-height: 1.7;
        margin-bottom: 3rem;
        color: var(--text-secondary);
    }

    .contact-methods {
        display: flex;
        flex-direction: column;
        gap: 2rem;
    }

    .contact-method h3 {
        font-size: 1.2rem;
        margin-bottom: 0.5rem;
        color: var(--accent);
    }

    .contact-method a {
        display: inline-block;
        transition: color 0.2s ease;
    }

    .contact-method a:hover {
        color: var(--accent);
    }

    .social-links {
        display: flex;
        gap: 1.5rem;
    }

    .form-group {
        margin-bottom: 1.5rem;
    }

    label {
        display: block;
        margin-bottom: 0.5rem;
        font-size: 0.9rem;
        color: var(--text-secondary);
    }

    input,
    textarea {
        width: 100%;
        padding: 0.8rem;
        background-color: var(--bg-secondary);
        border: 1px solid var(--border-color);
        border-radius: var(--border-radius);
        color: var(--text-primary);
        font-family: inherit;
        transition: border-color 0.2s ease;
    }

    input:focus,
    textarea:focus {
        outline: none;
        border-color: var(--accent);
    }

    button {
        width: 100%;
        margin-top: 1rem;
    }

    button:disabled {
        background-color: var(--bg-secondary);
        cursor: not-allowed;
    }

    .submit-success {
        margin-top: 1rem;
        padding: 1rem;
        background-color: rgba(111, 107, 224, 0.1);
        border-left: 3px solid var(--accent);
        border-radius: 4px;
        font-size: 0.9rem;
    }

    @media (max-width: 920px) {
        .contact-content {
            grid-template-columns: 1fr;
            gap: 3rem;
        }
    }
</style>