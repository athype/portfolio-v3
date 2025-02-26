<script>
    import ProjectCard from './ProjectCard.svelte';

    const projects = [
        {
            title: 'E-commerce Platform',
            description: 'A full-stack e-commerce solution with product management, user authentication, and payment integration.',
            image: 'https://images.unsplash.com/photo-1563013544-824ae1b704d3?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80',
            tags: ['React', 'Node.js', 'MongoDB', 'Stripe'],
            link: '#'
        },
        {
            title: 'Task Management App',
            description: 'A productivity application featuring drag-and-drop task organization, deadlines, and team collaboration.',
            image: 'https://images.unsplash.com/photo-1484480974693-6ca0a78fb36b?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80',
            tags: ['Vue.js', 'Firebase', 'Tailwind CSS'],
            link: '#'
        },
        {
            title: 'Travel Journal',
            description: 'A mobile application that helps users document their travels with photos, notes, and interactive maps.',
            image: 'https://images.unsplash.com/photo-1527631746610-bca00a040d60?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80',
            tags: ['React Native', 'GraphQL', 'AWS'],
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