<script>
    import ProjectCard from './ProjectCard.svelte';

    const projects = [
        {
            title: 'Basecrusher.com',
            description: 'A clash of clans army composition and guide website with a focus on user experience and performance.',
            image: 'src/assets/basecrusher.png',
            tags: ['Svelte', 'Node.js', 'ExpressJs', 'SQLite'],
            link: 'https://basecrusher.com'
        },
        {
            title: 'Scorion Feedback system',
            description: 'A proof of concept feedback system for Scorion using OpenAI Whisper. Developed as a group project',
            image: 'src/assets/feedback.png',
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
</style>