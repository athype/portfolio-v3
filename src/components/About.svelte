<script>
    import { onMount } from 'svelte';
    import { gsap } from 'gsap';
    import SplitType from 'split-type';

    const skills = [
        { category: 'Frontend', items: ['JS/TS', 'Svelte', 'Vue', 'React-Native'] },
        { category: 'Backend', items: ['JS/TS', 'Java', 'ExpressJs', 'NestJS', 'Prisma'] },
        { category: 'Tools', items: ['Git', 'Docker', 'AWS', 'Azure', 'Gitlab CI/CD', 'Figma'] }
    ];

    let skillsRef;

    onMount(() => {
        // --- Scroll-driven bio text reveal ---
        const bioParas = document.querySelectorAll('.about-text p');
        bioParas.forEach((p) => {
            const split = new SplitType(/** @type {HTMLElement} */ (p), { types: 'words' });
            split.words.forEach((word) => {
                word.style.willChange = 'opacity';
            });

            gsap.fromTo(split.words,
                { opacity: 0.12 },
                {
                    opacity: 1,
                    stagger: 0.05,
                    scrollTrigger: {
                        trigger: p,
                        start: 'top 85%',
                        end: 'top 25%',
                        scrub: true,
                    }
                }
            );
        });

        // --- Staggered skill entrance ---
        const groups = skillsRef.querySelectorAll('.skill-group');
        groups.forEach((group, gi) => {
            const heading = group.querySelector('h3');
            const items = group.querySelectorAll('li');

            gsap.from(heading, {
                opacity: 0,
                x: -20,
                duration: 0.5,
                delay: gi * 0.15,
                scrollTrigger: {
                    trigger: group,
                    start: 'top 85%',
                }
            });

            gsap.from(items, {
                opacity: 0,
                x: -20,
                stagger: 0.08,
                duration: 0.4,
                delay: gi * 0.15 + 0.2,
                scrollTrigger: {
                    trigger: group,
                    start: 'top 85%',
                }
            });
        });
    });
</script>

<section id="about" class="section">
    <div class="container">
        <h2>About Me</h2>

        <div class="about-content">
            <div class="about-text fade-in">
                <p>I'm a passionate software engineer with a focus on creating elegant, user-focused digital experiences. With expertise in both frontend and backend development, I enjoy working across the entire stack to build cohesive applications.</p>

                <p>My journey started with web development, but has expanded to include mobile applications, interactive experiences, and exploring emerging technologies. I'm particularly interested in the intersection of design and development.</p>

                <p>When I'm not working, you can find me exploring new technologies or experimenting with creative coding projects.</p>
            </div>

            <div class="about-skills fade-in">
                <div class="skills-container" bind:this={skillsRef}>
                    {#each skills as skillGroup}
                        <div class="skill-group">
                            <h3>{skillGroup.category}</h3>
                            <ul>
                                {#each skillGroup.items as skill}
                                    <li>{skill}</li>
                                {/each}
                            </ul>
                        </div>
                    {/each}
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

    .about-content {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 4rem;
    }

    .about-text p {
        margin-bottom: 1.5rem;
        font-size: 1.1rem;
        line-height: 1.7;
    }

    .skills-container {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 2rem;
    }

    .skill-group h3 {
        font-size: 1.2rem;
        margin-bottom: 1rem;
        color: var(--accent);
    }

    .skill-group ul {
        list-style: none;
    }

    .skill-group li {
        margin-bottom: 0.5rem;
        position: relative;
        padding-left: 1.5rem;
    }

    .skill-group li::before {
        content: '→';
        position: absolute;
        left: 0;
        color: var(--accent);
    }

    /* Force visibility */
    :global(.fade-in) {
        opacity: 1 !important;
    }

    @media (max-width: 920px) {
        .about-content {
            grid-template-columns: 1fr;
        }

        .skills-container {
            grid-template-columns: 1fr;
        }
    }
</style>