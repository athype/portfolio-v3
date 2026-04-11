<script>
    import ProjectCard from './ProjectCard.svelte';

    const projects = [
        {
            title: 'Drink administration platform',
            description: 'A platform for managing and tracking drink consumption, built with Vue and Node.js.',
            tags: ['Vue', 'Node.js', 'ExpressJs', 'PostgreSQL', 'Docker', 'Webauthn'],
            image: '/beermachine.png',
            link: 'https://beer.sv-ada.nl'
        },
        {
            title: 'Alchemix',
            description: 'A hybrid app for cocktail recipes and inventory keeping, built with React Native and NestJS.',
            tags: ['React Native', 'NestJS', 'PostgreSQL', 'Docker', 'Coolify'],
            image: '/alchemix.png',
            link: 'https://alchemix.krisztiankozari.com/'
        },
        {
            title: 'Zenclash.ing',
            description: 'A clash of clans clan family landing page and war line-up management webapp.',
            tags: ['Vue', 'Node.js', 'ExpressJs', 'Docker', 'Discord oAuth', 'Clash of Clans API', 'Clash King API'],
            link: 'https://zenclash.ing'
        },
        {
            title: 'Basecrusher.com',
            description: 'A clash of clans army composition and guide website with a focus on user experience and performance.',
            image: '/basecrusher.png',
            tags: ['Svelte', 'Node.js', 'ExpressJs', 'SQLite'],
            link: 'https://basecrusher.com'
        },
        {
            title: 'Scorion Feedback system',
            description: 'A proof of concept feedback system for Scorion using OpenAI Whisper. Developed as a group project',
            image: '/feedback.png',
            tags: ['Svelte', 'ExpressJs', 'OpenAI Whisper', "SQLite", "Node.js"],
            link: '#'
        }
    ];
</script>

<section id="projects" class="section">
    <div class="container">
        <h2 class="split-text">Selected Projects</h2>

        <div class="projects-grid">
            {#each projects as project, i}
                <ProjectCard {...project} index={i} />
            {/each}
        </div>

        <div class="more-projects fade-in">
            <p>Interested in seeing more?</p>
            <a href="https://github.com/athype" target="_blank" rel="noopener noreferrer" class="button">
                View GitHub Profile
            </a>
        </div>
    </div>
</section>

<style>
    h2 {
        font-size: 3rem;
        margin-bottom: 3rem;
    }

    .projects-grid {
        display: grid;
        grid-template-columns: 1fr;
        gap: 6rem;
        margin-bottom: 4rem;
    }

    .more-projects {
        text-align: center;
        margin-top: 6rem;
    }

    .more-projects p {
        font-size: 1.2rem;
        margin-bottom: 1.5rem;
        color: var(--text-secondary);
    }
    .button {
        color: #0a192f;
    }
</style>