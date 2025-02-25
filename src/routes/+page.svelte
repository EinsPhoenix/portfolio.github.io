<script lang="ts">
    import { base } from '$app/paths';
    import { onMount } from 'svelte';
    
    let activeSlide = 0;
    let carouselContainer: HTMLElement;
    let nextSection: HTMLElement;
    const slides = [1, 2, 3, 4]; 
    let isScrolling = false;
    let carouselActive = true;
    
    const handleWheel = (e: WheelEvent) => {
      
      if (carouselActive) {
        e.preventDefault();
      }
      
      if (isScrolling) return;
      isScrolling = true;
      
      const delta = e.deltaY;
      
      if (delta > 0) { // Runter scrollen
        if (activeSlide < slides.length - 1) {
          
          activeSlide++;
        } else if (carouselActive) {
          
          carouselActive = false;
          
          
          window.scrollTo({
            top: window.innerHeight,
            behavior: 'smooth'
          });
          
          
          document.body.style.overflow = 'auto';
        }
      } else if (delta < 0) { // Hoch scrollen
        if (window.scrollY > 0 && window.scrollY < window.innerHeight) {
         
          window.scrollTo({
            top: 0,
            behavior: 'smooth'
          });
          carouselActive = true;
          
          
          document.body.style.overflow = 'hidden';
        } else if (carouselActive && activeSlide > 0) {
         
          activeSlide--;
        }
      }
      
      setTimeout(() => {
        isScrolling = false;
        
       
        if (window.scrollY === 0) {
          carouselActive = true;
          document.body.style.overflow = 'hidden';
        } else if (window.scrollY >= window.innerHeight) {
          carouselActive = false;
          document.body.style.overflow = 'auto';
        }
      }, 800);
    };
    
    onMount(() => {
      if (carouselContainer) {
        
        window.addEventListener('wheel', handleWheel, { passive: false });
        
       
        document.body.style.overflow = 'hidden';
        
        return () => {
          window.removeEventListener('wheel', handleWheel);
          document.body.style.overflow = 'auto';
        };
      }
    });
  </script>
  
  <style>
    .carousel {
      width: 100%;
      height: 100vh;
      overflow: hidden;
      position: relative;
    }
    .slides {
      display: flex;
      height: 100%;
      transition: transform 0.5s ease-in-out;
    }
    .slide {
      flex: 0 0 100%;
      height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 4rem;
    }
    .next-section {
      height: 100vh;
      padding: 2rem;
      
      margin-top: 0;
    }
    :global(body) {
      margin: 0;
      padding: 0;
    }
  </style>
  
  <!-- Carousel -->
  <div class="carousel" bind:this={carouselContainer}>
    <div class="slides" style="transform: translateX(-{activeSlide * 100}%)">
      {#each slides as _, i}
        <div class="slide" style="background: hsl({i * 70}, 60%, 60%)">
          Slide {i + 1}
        </div>
      {/each}
    </div>
  </div>
  
  <!-- Nächster Abschnitt -->
  <div class="next-section" bind:this={nextSection}>
    <h2>Nächster Seitenabschnitt</h2>
    <p>Hier kommt der restliche Inhalt...</p>
  </div>

  <!-- Nächster Abschnitt -->
  <div class="next-section" bind:this={nextSection}>
    <h2>Nächster Seitenabschnitt</h2>
    <p>Hier kommt der restliche Inhalt...</p>
  </div>