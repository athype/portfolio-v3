<script>
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';

  import Header from './components/Header.svelte';
  import Hero from './components/Hero.svelte';
  import About from './components/About.svelte';
  import Projects from './components/Projects.svelte';
  import Contact from './components/Contact.svelte';
  import Footer from './components/Footer.svelte';
  import Cursor from './components/Cursor.svelte';

  import './styles/global.css';
  import DebugHelper from "./components/DebugHelper.svelte";
  import RadialMenu from "./components/RadialMenu.svelte";

  onMount(() => {
    console.log("App mounted - initializing ScrollTrigger");

    // Register GSAP plugin
    gsap.registerPlugin(ScrollTrigger);

    // Delay scroll trigger initialization
    setTimeout(() => {
      // Basic fade animations for sections (using classes instead of complex selectors)
      const sections = document.querySelectorAll('.section');
      console.log(`Found ${sections.length} sections`);

      sections.forEach((section, index) => {
        console.log(`Setting up animation for section ${index}`);

        // Animate the section title
        const title = section.querySelector('h2');
        if (title) {
          gsap.from(title, {
            opacity: 0,
            y: 30,
            duration: 0.8,
            scrollTrigger: {
              trigger: section,
              start: 'top 80%',
              markers: false // Add markers for debugging
            }
          });
        }

        // Animate fade-in elements within this section
        const fadeElements = section.querySelectorAll('.fade-in');
        fadeElements.forEach((el, i) => {
          gsap.from(el, {
            opacity: 0,
            y: 20,
            duration: 0.8,
            delay: 0.1 * i,
            scrollTrigger: {
              trigger: section,
              start: 'top 70%'
            }
          });
        });
      });

      // Remove markers after debugging
      setTimeout(() => {
        document.querySelectorAll('.gsap-marker-start, .gsap-marker-end, .gsap-marker-scroller-start, .gsap-marker-scroller-end')
                .forEach(el => el.remove());
      }, 5000);
    }, 500); // Small delay to ensure DOM is ready
  });
</script>

<Cursor />
<Header />
<main>
  <Hero />
  <About />
  <Projects />
  <Contact />
</main>
<Footer />
<RadialMenu />

<style>
  main {
    min-height: 100vh;
  }

  /* Ensure sections are visible by default */
  :global(.section) {
    opacity: 1;
    min-height: 50vh;
    padding: 5rem 0;
  }
</style>