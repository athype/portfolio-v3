<script>
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';
  import Lenis from 'lenis';
  import SplitType from 'split-type';

  import Header from './components/Header.svelte';
  import Hero from './components/Hero.svelte';
  import About from './components/About.svelte';
  import Projects from './components/Projects.svelte';
  import Contact from './components/Contact.svelte';
  import Footer from './components/Footer.svelte';
  import Cursor from './components/Cursor.svelte';

  import './styles/global.css';
  import DebugHelper from "./components/DebugHelper.svelte";

  let lenis;

  onMount(() => {
    // Register GSAP plugin
    gsap.registerPlugin(ScrollTrigger);

    // --- Lenis smooth scroll ---
    lenis = new Lenis({
      duration: 1.2,
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
      smoothWheel: true,
    });

    // Connect Lenis to GSAP ScrollTrigger
    lenis.on('scroll', ScrollTrigger.update);
    gsap.ticker.add((time) => {
      lenis.raf(time * 1000);
    });
    gsap.ticker.lagSmoothing(0);

    // Delay scroll trigger initialization
    setTimeout(() => {
      // --- Split-type text reveal animations ---
      const splitHeadings = document.querySelectorAll('.section h2');
      splitHeadings.forEach((heading) => {
        const split = new SplitType(/** @type {HTMLElement} */ (heading), { types: 'chars,words' });

        gsap.from(split.chars, {
          opacity: 0,
          y: 40,
          rotateX: -90,
          stagger: 0.02,
          duration: 0.8,
          ease: 'back.out(1.7)',
          scrollTrigger: {
            trigger: heading,
            start: 'top 85%',
          }
        });
      });

      // Basic fade animations for sections
      const sections = document.querySelectorAll('.section');

      sections.forEach((section, index) => {
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
    }, 500);

    // --- Magnetic buttons ---
    const magneticElements = document.querySelectorAll('.btn, .button, .copy-btn, .scroll-top');
    magneticElements.forEach((el) => {
      el.classList.add('magnetic');

      el.addEventListener('mousemove', (/** @type {MouseEvent} */ e) => {
        const rect = el.getBoundingClientRect();
        const x = e.clientX - rect.left - rect.width / 2;
        const y = e.clientY - rect.top - rect.height / 2;

        gsap.to(el, {
          x: x * 0.3,
          y: y * 0.3,
          duration: 0.3,
          ease: 'power2.out'
        });
      });

      el.addEventListener('mouseleave', () => {
        gsap.to(el, {
          x: 0,
          y: 0,
          duration: 0.5,
          ease: 'elastic.out(1, 0.3)'
        });
      });
    });
  });

  onDestroy(() => {
    if (lenis) {
      lenis.destroy();
    }
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