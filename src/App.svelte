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
  import Preloader from './components/Preloader.svelte';
  import Marquee from './components/Marquee.svelte';

  import './styles/global.css';
  import DebugHelper from "./components/DebugHelper.svelte";

  let lenis;
  let loaded = false;

  function handlePreloaderComplete() {
    loaded = true;
  }

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

    // --- Scroll progress indicator ---
    gsap.to('.scroll-progress', {
      scaleX: 1,
      ease: 'none',
      scrollTrigger: {
        trigger: document.body,
        start: 'top top',
        end: 'bottom bottom',
        scrub: 0.3,
      }
    });
  });

  onDestroy(() => {
    if (lenis) {
      lenis.destroy();
    }
  });
</script>

<Preloader onComplete={handlePreloaderComplete} />

<div class="scroll-progress"></div>
<div class="noise-overlay"></div>

<Cursor />
<Header />
<main>
  <Hero />
  <Marquee />
  <About />
  <Marquee direction="right" items={['Creative Development', 'Full Stack', 'UI/UX', 'Interactive Experiences', 'Web Applications', 'Mobile Apps', 'Cloud Infrastructure', 'API Design']} speed={35} />
  <Projects />
  <Contact />
</main>
<div class="footer-reveal">
  <Footer />
</div>

<style>
  main {
    min-height: 100vh;
    position: relative;
    z-index: 2;
    background: var(--bg-primary);
  }

  /* Footer reveal — footer sits behind main */
  .footer-reveal {
    position: sticky;
    bottom: 0;
    z-index: 1;
  }

  /* Scroll progress bar */
  .scroll-progress {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 3px;
    background: var(--accent);
    transform-origin: left;
    transform: scaleX(0);
    z-index: 10000;
  }

  /* Ensure sections are visible by default */
  :global(.section) {
    opacity: 1;
    min-height: 50vh;
    padding: 5rem 0;
  }

  /* Film grain / noise overlay */
  .noise-overlay {
    position: fixed;
    top: -10%;
    left: -10%;
    width: 120%;
    height: 120%;
    z-index: 9998;
    pointer-events: none;
    opacity: 0.018;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
    animation: grain 0.8s steps(1) infinite;
  }

  @keyframes grain {
    0%, 100% { transform: translate(0, 0); }
    10% { transform: translate(-2%, -3%); }
    20% { transform: translate(-4%, 2%); }
    30% { transform: translate(3%, -4%); }
    40% { transform: translate(-2%, 4%); }
    50% { transform: translate(-4%, 3%); }
    60% { transform: translate(4%, 0%); }
    70% { transform: translate(0%, 3%); }
    80% { transform: translate(2%, -2%); }
    90% { transform: translate(-3%, 2%); }
  }
</style>