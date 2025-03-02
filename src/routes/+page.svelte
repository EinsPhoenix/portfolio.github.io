<script lang="ts">
  import { onMount } from "svelte";
  import { base } from "$app/paths";
  import type { SvelteHTMLElements } from "svelte/elements";

  // Carousel control
  let activeSlide = 0;
  let carouselContainer: HTMLElement;
  let isScrolling = false;
  let carouselActive = true;
  let skillsShowcaseVisible = false;

  // Chat bubble control
  let isChatOpen = false;

  // Animations
  let skillsVisible = false;
  let storyVisible = false;

  // Slides for the carousel
  const slides = [
    {
      title: "About me",
      subtitle: "Developer • Security Enthusiast • Problem Solver",
      content: "profile",
    },
    {
      title: "My Skills",
      subtitle: "Languages, Technologies & Frameworks",
      content: "skills",
    },
    {
      title: "My Education",
      subtitle: "Learning Path & Professional Development",
      content: "education",
    },
    {
      title: "My Activities",
      subtitle: "Hobbies, Engagement & Qualifications",
      content: "activities",
    },
    {
      title: "My Projects",
      subtitle: "What I've Been Working On",
      content: "projects",
    },
  ];

  // Skills by categories
  const skillCategories = [
    {
      name: "Programming Languages",
      skills: [
        "C#",
        "C++",
        "Python",
        "HTML",
        "CSS/SCSS",
        "JavaScript",
        "TypeScript",
        "Lua",
        "Rust",
      ],
    },
    {
      name: "Databases",
      skills: ["SQL", "SQLite", "Neo4j", "MongoDB"],
    },
    {
      name: "Frameworks",
      skills: [
        "Django",
        "Node.js",
        "Next.js",
        "SvelteKit",
        "React",
        "Atrix-web",
        "Qwik",
      ],
    },
    {
      name: "Engines & Tools",
      skills: [
        "Unity",
        "Omniverse",
        "Godot",
        "Roblox Studio",
        "Blender",
        "Git",
        "Bitbucket",
        "Jira",
        "Docker",
      ],
    },
    {
      name: "Operating Systems",
      skills: ["Linux (Ubuntu, Arch)", "Windows"],
    },
  ];

  // Education timeline
  const educationTimeline = [
    {
      period: "2023 - 2026",
      title: "Training as IT Specialist",
      description: "Application Development at Feynsinn Edag",
      current: true,
    },
    {
      period: "2022 - 2023",
      title: "Civil Engineering Studies",
      description: "Not as exciting as my computer science lectures",
    },
    {
      period: "2020 - 2022",
      title: "Technical High School",
      description: "Graduated with focus on Mechanical Engineering",
    },
  ];

  // Activities and engagements
  const activities = [
    {
      year: "2021",
      title: "Regional Management VCP-Kurhessen",
      icon: "users",
    },
    {
      year: "Since 2020",
      title: "Homelab & Pentesting",
      icon: "shield-alt",
    },
    {
      year: "2018",
      title: "Scout Leader Martin von Tours",
      icon: "compass",
    },
    {
      year: "2017",
      title: "School Paramedic",
      icon: "first-aid",
    },
    {
      year: "Since 2016",
      title: "Volunteer Fire Department Bad Salzschlirf",
      icon: "fire-extinguisher",
    },
    {
      year: "Since 2014",
      title: "Development with C# and Unity 3D",
      icon: "code",
    },
  ];

  // Projects
  const projects = [
    {
      title: "EdLearn Platform",
      description:
        "A learning platform created during Hackathon 2024 (2nd place winner in the category code). Similar to SharPoint but focused on education, allowing users to save information, filter content, and create quizzes with the help of ki. Features social media interactions like Instagram.",
      tech: ["Python", "Django", "React"],
      links: [
        {
          label: "Backend Repository",
          url: "https://github.com/EinsPhoenix/Hackerton-Backend",
        },
        {
          label: "Frontend Repository",
          url: "https://github.com/EinsPhoenix/Hackerton-Frontend",
        },
      ],
      image: "/static/1727366847875.jpeg",
    },
  ];

  // Additional qualifications
  const qualifications = [
    "Customer Consulting",
    "Trade Show Experience (incl. Hannover Fair 2024)",
    "Project Management (2nd Place Hackathon 2024 in the category code)",
    "CAD Knowledge (CATIA, Inventor)",
    "Office & Excel Proficiency",
  ];

  // Viewport detection
  let isMobile = false;
  let viewportHeight = 0;
  let safeAreaBottom = 0;

  let skillsShowcaseSection: HTMLElement;
  let isAnimatingToShowcase = false;

  // Handler for mouse wheel
  const handleWheel = (e: WheelEvent) => {
    if (carouselActive || skillsShowcaseVisible) {
      e.preventDefault();
    }

    if (isScrolling || isAnimatingToShowcase) return;
    isScrolling = true;

    const delta = e.deltaY;

    if (delta > 0) {
      // Scroll down
      if (activeSlide < slides.length - 1) {
        activeSlide++;
      } else if (carouselActive) {
        // We've reached the end of the carousel
        isAnimatingToShowcase = true;
        carouselActive = false;

        if (skillsShowcaseSection) {
          skillsShowcaseSection.scrollIntoView({ behavior: "smooth" });
        } else {
          window.scrollTo({ top: window.innerHeight, behavior: "smooth" });
        }

        document.body.style.overflow = "auto";
        skillsShowcaseVisible = true;

        setTimeout(() => {
          isAnimatingToShowcase = false;
        }, 1000);
      }
    } else if (delta < 0) {
      // Scroll up
      if (carouselActive) {
        if (window.scrollY > 0 && window.scrollY < window.innerHeight) {
          window.scrollTo({ top: 0, behavior: "smooth" });
          carouselActive = true;
          document.body.style.overflow = "hidden";
        } else if (activeSlide > 0) {
          activeSlide--;
        }
      } else {
        if (window.scrollY <= window.innerHeight + 50) {
          window.scrollTo({ top: 0, behavior: "smooth" });
          carouselActive = true;
          skillsShowcaseVisible = false;
          document.body.style.overflow = "hidden";
          e.preventDefault();
        }
      }
    }

    setTimeout(() => {
      isScrolling = false;
      if (window.scrollY === 0) {
        carouselActive = true;
        document.body.style.overflow = "hidden";
      } else if (window.scrollY >= window.innerHeight) {
        carouselActive = false;
        document.body.style.overflow = "auto";
      }
    }, 800);
  };

  let touchStartY = 0;
  let touchStartX = 0;

  const handleTouchStart = (e: TouchEvent) => {
    touchStartY = e.touches[0].clientY;
    touchStartX = e.touches[0].clientX;
  };

  const handleTouchMove = (e: TouchEvent) => {
    if (!carouselActive || isScrolling || isAnimatingToShowcase) return;

    const touchY = e.touches[0].clientY;
    const touchX = e.touches[0].clientX;

    const diffY = touchStartY - touchY;
    const diffX = touchStartX - touchX;

    if (Math.abs(diffY) > Math.abs(diffX)) {
      e.preventDefault();

      if (diffY > 50) {
        // Swipe up
        if (activeSlide < slides.length - 1) {
          activeSlide++;
          isScrolling = true;
          setTimeout(() => {
            isScrolling = false;
          }, 800);
        } else {
          // Last slide - show skills showcase
          isAnimatingToShowcase = true;
          carouselActive = false;

          if (skillsShowcaseSection) {
            skillsShowcaseSection.scrollIntoView({ behavior: "smooth" });
          } else {
            window.scrollTo({ top: window.innerHeight, behavior: "smooth" });
          }

          document.body.style.overflow = "auto";
          skillsShowcaseVisible = true;

          setTimeout(() => {
            isAnimatingToShowcase = false;
          }, 1000);
        }
      } else if (diffY < -50) {
        // Swipe down
        if (activeSlide > 0) {
          activeSlide--;
          isScrolling = true;
          setTimeout(() => {
            isScrolling = false;
          }, 800);
        }
      }
    }
  };

  const animateContent = () => {
    skillsVisible = false;
    storyVisible = false;

    if (activeSlide === 0) {
      setTimeout(() => {
        storyVisible = true;
      }, 300);
    } else if (activeSlide === 1) {
      setTimeout(() => {
        skillsVisible = true;
      }, 300);
    }
  };

  const nextSlide = () => {
    if (activeSlide < slides.length - 1) {
      activeSlide++;
    } else {
      // Last slide - show skills showcase on next button
      isAnimatingToShowcase = true;
      carouselActive = false;

      if (skillsShowcaseSection) {
        skillsShowcaseSection.scrollIntoView({ behavior: "smooth" });
      } else {
        window.scrollTo({ top: window.innerHeight, behavior: "smooth" });
      }

      document.body.style.overflow = "auto";
      skillsShowcaseVisible = true;

      setTimeout(() => {
        isAnimatingToShowcase = false;
      }, 1000);
    }
  };

  const prevSlide = () => {
    if (activeSlide > 0) {
      activeSlide--;
    }
  };

  const openLinkedIn = () => {
    window.open("https://www.linkedin.com/in/noah-kirsch-4bb2112b0/", "_blank");
  };

  const checkViewport = () => {
    isMobile = window.innerWidth <= 768;
    viewportHeight = window.innerHeight;

    safeAreaBottom = 0;
    if (
      window.CSS &&
      CSS.supports("padding-bottom: env(safe-area-inset-bottom)")
    ) {
      const computedStyle = getComputedStyle(document.documentElement);
      const safeAreaValue = computedStyle
        .getPropertyValue("--safe-area-inset-bottom")
        .trim();
      if (safeAreaValue) {
        safeAreaBottom = parseInt(safeAreaValue, 10) || 0;
      }
    }
  };

  let wheelHandler: (e: WheelEvent) => void;
  let touchStartHandler: (e: TouchEvent) => void;
  let touchMoveHandler: (e: TouchEvent) => void;
  let resizeHandler: () => void;
  let scrollHandler: () => void;

  onMount(() => {
    try {
      setTimeout(() => {
        if (typeof window === "undefined" || !document.body) return;

        wheelHandler = (e) => handleWheel(e);
        touchStartHandler = (e) => handleTouchStart(e);
        touchMoveHandler = (e) => handleTouchMove(e);
        resizeHandler = checkViewport;
        scrollHandler = () => {
          if (window.scrollY < 50 && !carouselActive) {
            carouselActive = true;
            document.body.style.overflow = "hidden";
          }
        };

        if (carouselContainer) {
          window.addEventListener("wheel", wheelHandler, { passive: false });
          window.addEventListener("touchstart", touchStartHandler, {
            passive: true,
          });
          window.addEventListener("touchmove", touchMoveHandler, {
            passive: false,
          });
          window.addEventListener("resize", resizeHandler);
          document.body.style.overflow = "hidden";

          const showcaseElement = document.querySelector(".skills-showcase");
          if (showcaseElement) {
            skillsShowcaseSection = showcaseElement as HTMLElement;
          }

          checkViewport();
          animateContent();

          function updateVhVariable() {
            let vh = window.innerHeight * 0.01;
            document.documentElement.style.setProperty("--vh", `${vh}px`);
          }

          updateVhVariable();

          window.addEventListener("resize", updateVhVariable);

          window.addEventListener("scroll", scrollHandler);
        } else {
          window.addEventListener("resize", initializeSkillCards);
          if (skillsShowcaseVisible) {
            initializeSkillCards();
          }
        }
      }, 200);
    } catch (error) {
      console.error("Error during initialization:", error);
    }

    return () => {
      try {
        if (typeof window !== "undefined") {
          if (wheelHandler) window.removeEventListener("wheel", wheelHandler);
          if (touchStartHandler)
            window.removeEventListener("touchstart", touchStartHandler);
          if (touchMoveHandler)
            window.removeEventListener("touchmove", touchMoveHandler);
          if (resizeHandler)
            window.removeEventListener("resize", resizeHandler);
          if (scrollHandler)
            window.removeEventListener("scroll", scrollHandler);
          window.removeEventListener("resize", updateVhVariable);
        }
        if (document.body) document.body.style.overflow = "auto";
      } catch (error) {
        console.error("Error cleaning up event listeners:", error);
      }
    };
  });

  function updateVhVariable() {
    if (typeof window !== "undefined") {
      let vh = window.innerHeight * 0.01;
      document.documentElement.style.setProperty("--vh", `${vh}px`);
    }
  }

  function initializeSkillCards() {
    if (typeof document === "undefined" || !document.body) return;

    const skillCards = document.querySelectorAll(".skill-card");
    const container = document.querySelector(".skills-cards-container");

    if (!container) return;

    skillCards.forEach((card) => {
      if (card) {
        const cardElement = card as HTMLElement;
        const randomRotate = Math.random() * 8 - 4;
        const randomOffset = Math.random() * 20 - 10;

        cardElement.style.transform = `rotate(${randomRotate}deg) translate(${randomOffset}px, ${randomOffset}px)`;
      }
    });
  }

  $: if (skillsShowcaseVisible && typeof document !== "undefined") {
    setTimeout(() => {
      const container = document.querySelector(".skills-cards-container");
      if (container) {
        initializeSkillCards();
      }
      if (typeof window !== "undefined") {
        window.dispatchEvent(new Event("resize"));
      }
    }, 300);
  }

  $: if (typeof activeSlide !== "undefined") {
    animateContent();
  }
  $: if (skillsShowcaseVisible && typeof window !== "undefined") {
    setTimeout(() => {
      initializeSkillCards();
      window.dispatchEvent(new Event("resize"));
    }, 300);
  }
</script>

<meta charset="UTF-8" />
<meta
  name="viewport"
  content="width=device-width, initial-scale=1.0, viewport-fit=cover"
/>
<title>Noah Kirsch - Portfolio</title>

<!-- Font Awesome -->
<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css"
/>

<!-- Google Fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" />
<link
  href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap"
  rel="stylesheet"
/>

<div class="carousel" bind:this={carouselContainer}>
  <div class="slides" style="transform: translateX(-{activeSlide * 100}%)">
    {#each slides as slide, i}
      <div class="slide">
        <div class="slide-bg">
          <div class="particles"></div>
        </div>
        <div class="slide-content">
          <h1 class="slide-title">{slide.title}</h1>
          <h2 class="slide-subtitle">{slide.subtitle}</h2>

          <div class="card">
            {#if slide.content === "profile"}
              <!-- Personal information with storytelling approach -->
              <div class="profile">
                <div
                  class="profile-img"
                  on:click={openLinkedIn}
                  role="button"
                  tabindex="0"
                  on:keydown={(e) => e.key === "Enter" && openLinkedIn()}
                >
                  <img src="{base}/1727366847875.jpeg" alt="Noah Kirsch" />
                  <div class="profile-social-overlay">
                    <i class="fab fa-linkedin"></i>
                  </div>
                </div>
                <div class="profile-info">
                  <h3
                    class="profile-name"
                    on:click={openLinkedIn}
                    role="button"
                    tabindex="0"
                    on:keydown={(e) => e.key === "Enter" && openLinkedIn()}
                  >
                    Noah Kirsch
                  </h3>
                  <p class="profile-title">
                    IT Specialist for Application Development (in training)
                  </p>

                  <div class="story-container" class:visible={storyVisible}>
                    <p class="story-text">
                      Hello! I'm a passionate programmer from Fulda, Bad
                      Salzschlirf with a strong interest in security and
                      consulting. My journey in tech started when I was 11, and
                      since then I've been exploring various programming
                      languages and frameworks.
                    </p>
                    <p class="story-text">
                      Currently, I'm training as an IT Specialist at Feynsinn
                      Edag, where I'm expanding my skills in application
                      development and working on exciting projects at the
                      intersection of technology and problem-solving.
                    </p>
                    <p class="story-text">
                      Outside of coding, I've been involved with scouts,
                      volunteer firefighting, and running my own homelab for
                      security testing. I'm always looking for new challenges
                      and opportunities to grow.
                    </p>
                    <div class="contact-button">
                      <a href="mailto:noahkirsch06@gmail.com">
                        <i class="fas fa-envelope"></i> Contact Me
                      </a>
                    </div>
                  </div>
                </div>
              </div>
            {:else if slide.content === "skills"}
              <!-- Skills and knowledge -->
              <div class="skills-container">
                {#each skillCategories as category, index}
                  <div
                    class="skill-category"
                    class:visible={skillsVisible}
                    style="animation-delay: {0.1 + index * 0.1}s"
                  >
                    <h3 class="skill-category-title">{category.name}</h3>
                    <div class="skill-items">
                      {#each category.skills as skill}
                        <div class="skill-item">{skill}</div>
                      {/each}
                    </div>
                  </div>
                {/each}
              </div>
            {:else if slide.content === "education"}
              <!-- Education -->
              <div class="timeline">
                {#each educationTimeline as item, index}
                  <div
                    class="timeline-item"
                    class:visible={activeSlide === 2}
                    style="animation-delay: {0.1 + index * 0.15}s"
                  >
                    <div class="timeline-dot"></div>
                    <div class="timeline-period">{item.period}</div>
                    <div class="timeline-title">{item.title}</div>
                    <div class="timeline-description">{item.description}</div>
                  </div>
                {/each}
              </div>
            {:else if slide.content === "activities"}
              <!-- Activities and qualifications -->
              <h3 class="section-subtitle">Engagement & Hobbies</h3>
              <div class="activities-grid">
                {#each activities as activity, index}
                  <div
                    class="activity-item"
                    class:visible={activeSlide === 3}
                    style="animation-delay: {0.1 + index * 0.05}s"
                  >
                    <div class="activity-icon">
                      <i class="fas fa-{activity.icon}"></i>
                    </div>
                    <div class="activity-year">{activity.year}</div>
                    <div class="activity-title">{activity.title}</div>
                  </div>
                {/each}
              </div>

              <h3 class="section-subtitle">Additional Qualifications</h3>
              <div class="qualifications-list">
                {#each qualifications as qualification}
                  <div class="qualification-item">{qualification}</div>
                {/each}
              </div>
            {:else if slide.content === "projects"}
              <!-- Projects -->
              <div class="projects-container">
                {#each projects as project, index}
                  <div
                    class="project-card"
                    class:visible={activeSlide === 4}
                    style="animation-delay: {0.1 + index * 0.15}s"
                  >
                    <div class="project-content">
                      <h3 class="project-title">{project.title}</h3>
                      <p class="project-description">{project.description}</p>

                      <div class="project-tech">
                        {#each project.tech as tech}
                          <span class="tech-badge">{tech}</span>
                        {/each}
                      </div>

                      <div class="project-links">
                        {#each project.links as link}
                          <a
                            href={link.url}
                            target="_blank"
                            class="project-link"
                          >
                            <i class="fab fa-github"></i>
                            {link.label}
                          </a>
                        {/each}
                      </div>
                    </div>
                  </div>
                {/each}
              </div>
            {/if}
          </div>
        </div>
      </div>
    {/each}
  </div>

  <!-- Navigation Dots -->
  <div class="nav-dots">
    {#each slides as _, i}
      <button
        class="nav-dot"
        class:active={activeSlide === i}
        on:click={() => (activeSlide = i)}
        aria-label={`Go to slide ${i + 1}`}
      ></button>
    {/each}
  </div>

  <!-- Navigation Arrows -->
  <button
    class="nav-arrow nav-prev"
    on:click={prevSlide}
    aria-label="Previous slide"
  >
    <i class="fas fa-chevron-left"></i>
  </button>
  <button
    class="nav-arrow nav-next"
    on:click={nextSlide}
    aria-label="Next slide"
  >
    <i class="fas fa-chevron-right"></i>
  </button>

  <!-- Scroll Hint -->
  <div class="scroll-hint" class:visible={activeSlide === slides.length - 1}>
    <span>Scroll down for more</span>
    <i class="fas fa-chevron-down"></i>
  </div>
</div>

<!-- Skills Showcase Section-->
<div class="skills-showcase" class:visible={skillsShowcaseVisible}>
  <div class="skills-showcase-background">
    <div class="binary-rain">
      {#each Array(30) as _, i}
        <div
          class="column"
          style="--x: {Math.random() * 100}%;
                        --speed: {15 + Math.random() * 15}s;
                        --delay: {Math.random() * 5}s;"
        >
          {#each Array(35) as _, j}
            <span class="bit" style="--hue: {Math.random() * 360}">
              {"01@$%&*#"[Math.floor(Math.random() * 15)]}
            </span>
          {/each}
        </div>
      {/each}
    </div>
  </div>

  <!-- Content -->
  <div class="showcase-header">
    <h2>Key Highlights</h2>
    <p>A glimpse of what I bring to the table</p>
  </div>

  <div class="skills-cards-container">
    <!-- Card 1 -->
    <div class="skill-card">
      <div class="skill-card-inner">
        <div class="skill-card-front">
          <div class="card-icon">
            <i class="fas fa-code"></i>
            <img src="{base}/coding.png" alt="Programming Experience" />
          </div>
        </div>
        <div class="skill-card-back">
          <h3>9+ Years</h3>
          <p>Programming Experience</p>
        </div>
      </div>
    </div>

    <!-- Card 2 -->
    <div class="skill-card">
      <div class="skill-card-inner">
        <div class="skill-card-front">
          <div class="card-icon">
            <i class="fas fa-handshake"></i>
            <img src="{base}/consulting.jpeg" alt="Consulting Experience" />
          </div>
        </div>
        <div class="skill-card-back">
          <h3>Consulting</h3>
          <p>Experience</p>
        </div>
      </div>
    </div>

    <!-- Card 3 -->
    <div class="skill-card">
      <div class="skill-card-inner">
        <div class="skill-card-front">
          <div class="card-icon">
            <i class="fas fa-server"></i>
            <img src="{base}/rust_neo4j.png" alt="Backend Development" />
          </div>
        </div>
        <div class="skill-card-back">
          <h3>Backend</h3>
          <p>Developer</p>
        </div>
      </div>
    </div>

    <!-- Card 4 -->
    <div class="skill-card">
      <div class="skill-card-inner">
        <div class="skill-card-front">
          <div class="card-icon">
            <i class="fas fa-vr-cardboard"></i>
            <img src="{base}/mixed_reality.jpg" alt="Mixed Reality" />
          </div>
        </div>
        <div class="skill-card-back">
          <h3>Mixed Reality</h3>
          <p>Developer</p>
        </div>
      </div>
    </div>

    <!-- Card 5 -->
    <div class="skill-card">
      <div class="skill-card-inner">
        <div class="skill-card-front">
          <img src="{base}/project.png" alt="Project Management" />
        </div>
        <div class="skill-card-back">
          <h3>Project</h3>
          <p>Management Experience</p>
        </div>
      </div>
    </div>

    <!-- Card 6 -->
    <div class="skill-card">
      <div class="skill-card-inner">
        <div class="skill-card-front">
          <img src="{base}/english1_2.png" alt="English Proficiency" />
        </div>
        <div class="skill-card-back">
          <h3>C1.2 Level</h3>
          <p>English Proficiency</p>
        </div>
      </div>
    </div>
  </div>
</div>
<!-- Chat Bubble -->
<button
  class="chat-bubble"
  on:click={() => (isChatOpen = !isChatOpen)}
  aria-label="Open contact chat"
>
  <img
    src="{base}/speaker_notes_200dp_E3E3E3_FILL0_wght400_GRAD0_opsz48.png"
    alt=""
    class="chat-icon"
    role="presentation"
  />
</button>

<!-- Chat Content -->
<div class="chat-content" class:open={isChatOpen}>
  <div class="chat-header">Contact</div>
  <div class="chat-info">
    <p><strong>Noah Kirsch</strong></p>
    <p>noahkirsch06@gmail.com</p>
  </div>
  <a href="mailto:noahkirsch06@gmail.com" class="chat-action">
    <i class="fas fa-envelope"></i> Send Message
  </a>
</div>

<style>
  /* Global Styles and Reset */
  :global(body) {
    margin: 0;
    padding: 0;
    font-family: "Poppins", "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    overflow-x: hidden;
    --primary-color: #5c44f8;
    --primary-dark: #382c85;
    --accent: #e74694;
    --secondary-color: #2ecc71;
    --text-color: #e1e1e6;
    --light-text: #a0a0a8;
    --dark-bg: #1a1a1a;
    --card-bg: rgba(30, 30, 36, 0.9);
    --card-shadow: 0 8px 30px rgba(0, 0, 0, 0.1);
    --vh: 1vh;
    --gradient: linear-gradient(135deg, #5d48e3, #e74694);
  }

  /* Carousel and Slides */
  .carousel {
    margin: 0;
    padding: 0;
    height: 100vh;
    height: calc(var(--vh, 1vh) * 100);
    position: relative;
    overflow: hidden;
  }

  .slides {
    display: flex;
    height: 100%;
    transition: transform 0.8s ease;
  }

  .slide {
    flex: 0 0 100%;
    height: 100%;
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden;
  }

  .slide-bg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(135deg, #111, #333);
    z-index: -1;
  }

  .particles {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image: radial-gradient(
        rgba(255, 255, 255, 0.15) 1px,
        transparent 1px
      ),
      radial-gradient(rgba(255, 255, 255, 0.1) 1px, transparent 1px);
    background-size:
      20px 20px,
      30px 30px;
    background-position:
      0 0,
      15px 15px;
  }

  .slide-content {
    width: 90%;
    max-width: 1200px;
    padding: 20px;
    z-index: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .slide-title {
    font-size: 2.8rem;
    font-weight: 800;
    margin-bottom: 0.5rem;
    text-align: center;
    text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
    opacity: 0;
    animation: fadeUp 0.8s forwards;

    background-image: var(--gradient);
    -webkit-background-clip: text;
    color: transparent;
  }

  .slide-subtitle {
    font-size: 1.2rem;
    font-weight: 400;
    margin-bottom: 2rem;
    color: var(--primary-color);
    text-align: center;
    text-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    opacity: 0;
    animation: fadeUp 0.8s 0.2s forwards;
  }

  /* Card Styles */
  .card {
    background: var(--card-bg);
    border-radius: 12px;
    box-shadow: var(--card-shadow);
    padding: 30px;
    width: 100%;
    max-width: 900px;
    max-height: 70vh;
    overflow-y: auto;
    opacity: 0;
    animation: fadeIn 0.8s 0.4s forwards;
  }

  /* Profile Section */
  .profile {
    display: flex;
    flex-direction: column;
    gap: 30px;
  }

  .profile-img {
    position: relative;
    width: 150px;
    height: 150px;
    overflow: hidden;
    border-radius: 50%;
    margin: 0 auto;
    cursor: pointer;
    transition:
      transform 0.3s ease,
      box-shadow 0.3s ease;
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
  }

  .profile-img:hover {
    transform: scale(1.05);
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
  }

  .profile-img img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .profile-social-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    display: flex;
    justify-content: center;
    align-items: center;
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  .profile-img:hover .profile-social-overlay {
    opacity: 1;
  }

  .profile-social-overlay i {
    color: white;
    font-size: 2rem;
  }

  .profile-info {
    text-align: center;
  }

  .profile-name {
    font-size: 2rem;
    font-weight: 700;
    margin-bottom: 5px;
    cursor: pointer;
    display: inline-block;
    color: var(--text-color);
    transition: color 0.3s ease;
  }

  .profile-name:hover {
    color: var(--primary-color);
  }

  .profile-title {
    font-size: 1.1rem;
    color: var(--accent);
    margin-bottom: 20px;
  }

  .story-container {
    opacity: 0;
    transform: translateY(20px);
    transition:
      opacity 0.8s ease,
      transform 0.8s ease;
  }

  .story-container.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .story-text {
    margin-bottom: 15px;
    line-height: 1.6;
    text-align: left;
    color: var(--light-text);
  }

  .contact-button {
    margin-top: 30px;
  }

  .contact-button a {
    display: inline-block;
    padding: 12px 25px;
    background: var(--primary-color);
    color: white;
    text-decoration: none;
    border-radius: 30px;
    font-weight: 600;
    transition:
      background 0.3s ease,
      transform 0.3s ease;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  }

  .contact-button a:hover {
    background: var(--primary-dark);
    transform: translateY(-3px);
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
  }

  .contact-button i {
    margin-right: 8px;
  }

  /* Skills Section */
  .skills-container {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 20px;
    width: 100%;
  }

  .skill-category {
    opacity: 0;
    transform: translateY(20px);
    transition:
      opacity 0.5s ease,
      transform 0.5s ease;
  }

  .skill-category.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .skill-category-title {
    font-size: 1.2rem;
    margin-bottom: 15px;
    color: var(--primary-dark);
    border-bottom: 2px solid var(--primary-color);
    padding-bottom: 5px;
  }

  .skill-items {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
  }

  .skill-item {
    background: var(--primary-color);
    padding: 8px 15px;
    border-radius: 20px;
    font-size: 0.9rem;
    color: var(--text-color);
    border: 1px solid rgba(52, 152, 219, 0.3);
    transition: all 0.3s ease;
  }

  .skill-item:hover {
    background: rgba(52, 152, 219, 0.2);
    transform: translateY(-2px);
  }

  /* Education Timeline */
  .timeline {
    position: relative;
    max-width: 700px;
    margin: 0 auto;
    padding: 20px 0;
  }

  .timeline::before {
    content: "";
    position: absolute;
    height: 100%;
    width: 2px;
    background-color: var(--primary-color);
    left: 50px;
    top: 0;
  }

  .timeline-item {
    position: relative;
    margin-bottom: 40px;
    padding-left: 80px;
    opacity: 0;
    transform: translateY(20px);
    transition:
      opacity 0.5s ease,
      transform 0.5s ease;
  }

  .timeline-item.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .timeline-dot {
    position: absolute;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background-color: var(--primary-color);
    left: 41px;
    top: 5px;
    z-index: 1;
  }

  .timeline-period {
    font-weight: 600;
    font-size: 0.9rem;
    color: var(--primary-color);
    margin-bottom: 5px;
  }

  .timeline-title {
    font-size: 1.3rem;
    font-weight: 700;
    margin-bottom: 5px;
    color: var(--text-color);
  }

  .timeline-description {
    font-size: 0.95rem;
    color: #666;
  }

  /* Activities Section */
  .section-subtitle {
    font-size: 1.5rem;
    color: var(--primary-color);
    margin: 30px 0 20px;
    text-align: center;
  }

  .activities-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 20px;
    margin-bottom: 40px;
  }

  .activity-item {
    background: var(--primary-dark);
    border-radius: 10px;
    padding: 20px;
    text-align: center;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    transition: all 0.3s ease;
    opacity: 0;
    transform: scale(0.9);
  }

  .activity-item.visible {
    opacity: 1;
    transform: scale(1);
  }

  .activity-item:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
  }

  .activity-icon {
    font-size: 2rem;
    color: var(--secondary-color);
    margin-bottom: 10px;
  }

  .activity-year {
    font-size: 0.8rem;
    font-weight: 600;
    color: #888;
    margin-bottom: 5px;
  }

  .activity-title {
    font-size: 1rem;
    font-weight: 600;
    color: var(--text-color);
  }

  .qualifications-list {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: center;
  }

  .qualification-item {
    background: rgba(46, 204, 113, 0.1);
    padding: 10px 20px;
    border-radius: 20px;
    font-size: 0.95rem;
    color: #27ae60;
    border: 1px solid rgba(46, 204, 113, 0.3);
    transition: all 0.3s ease;
  }

  .qualification-item:hover {
    background: rgba(46, 204, 113, 0.2);
    transform: translateY(-2px);
  }

  /* Projects Section */
  .projects-container {
    display: grid;
    grid-template-columns: 1fr;
    gap: 30px;
  }

  .project-card {
    background: var(--card-bg);
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
    transition: all 0.3s ease;
    opacity: 0;
    transform: translateY(20px);
  }

  .project-card.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .project-card:hover {
    transform: translateY(-5px) scale(1.02);
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.15);
  }

  .project-content {
    padding: 25px;
  }

  .project-title {
    font-size: 1.4rem;
    margin-bottom: 10px;
    color: var(--text-color);
  }

  .project-description {
    font-size: 0.95rem;
    color: #666;
    margin-bottom: 20px;
    line-height: 1.6;
  }

  .project-tech {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
  }

  .tech-badge {
    background: var(--primary-dark);
    padding: 5px 12px;
    border-radius: 15px;
    font-size: 0.8rem;
    color: var(--text-color);
  }

  .project-links {
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
  }

  .project-link {
    display: inline-flex;
    align-items: center;
    padding: 8px 15px;
    background: var(--primary-color);
    color: white;
    text-decoration: none;
    border-radius: 20px;
    font-size: 0.9rem;
    transition: all 0.3s ease;
  }

  .project-link:hover {
    background: var(--primary-dark);
    transform: translateY(-2px);
  }

  .project-link i {
    margin-right: 8px;
  }

  /* Navigation Elements */
  .nav-dots {
    position: absolute;
    bottom: 30px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 15px;
    z-index: 10;
  }

  .nav-dot {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.3);
    border: none;
    cursor: pointer;
    transition: all 0.3s ease;
  }

  .nav-dot.active {
    background: var(--primary-color);
    transform: scale(1.2);
  }

  .nav-dot:hover {
    background: rgba(255, 255, 255, 0.6);
  }

  .nav-arrow {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: rgba(255, 255, 255, 0.2);
    border: none;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    color: white;
    font-size: 1.2rem;
    z-index: 10;
    transition: all 0.3s ease;
  }

  .nav-arrow:hover {
    background: rgba(255, 255, 255, 0.4);
  }

  .nav-prev {
    left: 20px;
  }

  .nav-next {
    right: 20px;
  }

  .scroll-hint {
    position: absolute;
    bottom: 80px;
    left: 50%;
    transform: translateX(-50%);
    color: white;
    font-size: 0.9rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    opacity: 0;
    transition: opacity 0.5s ease;
    z-index: 5;
  }

  .scroll-hint.visible {
    opacity: 1;
    animation: bounce 2s infinite;
  }

  .scroll-hint i {
    margin-top: 10px;
    font-size: 1.2rem;
  }

  /* Skills Showcase Section */
  .skills-showcase {
    min-height: 100vh;
    background-color: var(--dark-bg);
    padding: 80px 20px;
    position: relative;
    opacity: 0;
    transition: opacity 0.8s ease;
    overflow: hidden;
  }

  .skills-showcase.visible {
    opacity: 1;
  }

  .skills-showcase-background {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 0;
    overflow: hidden;
  }

  .binary-rain {
    position: absolute;
    width: 100%;
    height: 100%;
    overflow: hidden;
    mix-blend-mode: screen;
    z-index: 1;
    pointer-events: none;
    font-family: "Courier New", monospace;
  }

  .column {
    position: absolute;
    left: var(--x);
    animation: fall var(--speed) var(--delay) linear infinite;
    transform: translateY(-100%);
  }

  .bit {
    position: relative;
    color: hsl(var(--hue), 80%, 65%);
    font-size: 1.4rem;
    font-weight: 700;
    opacity: 0.8;
    text-shadow: 0 0 10px currentColor;
    line-height: 1.2;
    display: block;
    animation: bit-fade 1.5s ease-in-out infinite;
  }

  .showcase-header {
    text-align: center;
    margin-bottom: 50px;
    position: relative;
    z-index: 1;
  }

  .showcase-header h2 {
    font-size: 2.8rem;
    font-weight: 800;
    margin-bottom: 0.5rem;
    text-align: center;
    text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
    opacity: 0;
    animation: fadeUp 0.8s forwards;

    background-image: var(--gradient);
    -webkit-background-clip: text;
    color: transparent;
  }

  .showcase-header p {
    font-size: 1.2rem;
    color: var(--primary-color);
  }

  .skills-cards-container {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 50px;
    max-width: 1200px;
    margin: 0 auto;
    position: relative;
    z-index: 1;
  }

  .skill-card {
    perspective: 1000px;
    height: 250px;
  }

  .skill-card-inner {
    position: relative;
    width: 100%;
    height: 100%;
    transition: transform 0.8s;
    transform-style: preserve-3d;
  }

  .skill-card:hover .skill-card-inner {
    transform: rotateY(180deg);
  }

  .skill-card-front,
  .skill-card-back {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    border-radius: 15px;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
  }

  .skill-card-front {
    background: linear-gradient(145deg, #232323, #333333);
  }

  .skill-card-back {
    background: linear-gradient(
      145deg,
      var(--primary-color),
      var(--primary-dark)
    );
    color: white;
    transform: rotateY(180deg);
    padding: 20px;
    text-align: center;
  }

  .skill-card-back h3 {
    font-size: 1.8rem;
    margin-bottom: 10px;
    font-weight: 800;
  }

  .skill-card-back p {
    font-size: 1.1rem;
  }

  .card-icon {
    position: relative;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .card-icon i {
    font-size: 4rem;
    color: rgba(255, 255, 255, 0.2);
    position: absolute;
  }

  .card-icon img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    opacity: 0.8;
    transition: opacity 0.3s ease;
  }

  .skill-card:hover .card-icon img {
    opacity: 0.5;
  }

  /* Chat Bubble */
  .chat-bubble {
    position: fixed;
    bottom: 30px;
    right: 30px;
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background: var(--primary-color);
    display: flex;
    justify-content: center;
    align-items: center;
    color: var(--primary-color);
    font-size: 1.5rem;
    border: none;
    cursor: pointer;
    z-index: 100;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
    transition: all 0.3s ease;
  }

  .chat-bubble:hover {
    transform: scale(1.1);
    background: var(--primary-dark);
  }

  .chat-icon {
    width: 30px;
    height: 30px;
    filter: invert(1);
  }

  .chat-content {
    position: fixed;
    bottom: 100px;
    right: 30px;
    width: 300px;
    background: var(--primary-color);
    border-radius: 15px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
    z-index: 99;
    transform: translateY(20px);
    opacity: 0;
    pointer-events: none;
    transition: all 0.3s ease;
    overflow: hidden;
  }

  .chat-content.open {
    transform: translateY(0);
    opacity: 1;
    pointer-events: all;
  }

  .chat-header {
    background: var(--primary-dark);
    color: white;
    padding: 15px 20px;
    font-weight: 600;
    font-size: 1.1rem;
  }

  .chat-info {
    padding: 20px;
  }

  .chat-info p {
    margin-bottom: 10px;
    color: var(--text-color);
  }

  .chat-action {
    display: block;
    text-align: center;
    padding: 15px;
    background: var(--primary-dark);
    color: var(--text-color);
    text-decoration: none;
    font-weight: 600;
    margin: 0 20px 20px;
    border-radius: 30px;
    transition: all 0.3s ease;
  }

  .chat-action:hover {
    background: var(--primary-dark);
    transform: translateY(-3px);
  }

  /* Animations */
  @keyframes fadeUp {
    from {
      opacity: 0;
      transform: translateY(20px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }

  @keyframes bounce {
    0%,
    20%,
    50%,
    80%,
    100% {
      transform: translateX(-50%) translateY(0);
    }
    40% {
      transform: translateX(-50%) translateY(-10px);
    }
    60% {
      transform: translateX(-50%) translateY(-5px);
    }
  }

  @keyframes fall {
    to {
      transform: translateY(150vh);
    }
  }

  @keyframes bit-fade {
    0%,
    100% {
      opacity: 0.1;
    }
    50% {
      opacity: 0.9;
    }
  }

  @keyframes glitch {
    0%,
    100% {
      transform: translateX(0);
    }
    20% {
      transform: translateX(-1px);
    }
    40% {
      transform: translateX(2px);
    }
    60% {
      transform: translateX(-1px);
    }
    80% {
      transform: translateX(1px);
    }
  }

  /* Responsive Media Queries */
  @media screen and (min-width: 768px) {
    .profile {
      flex-direction: row;
      align-items: center;
    }

    .profile-img {
      margin: 0;
    }

    .profile-info {
      text-align: left;
      flex: 1;
    }
  }

  @media screen and (max-width: 768px) {
    .slide-title {
      font-size: 2rem;
    }

    .slide-subtitle {
      font-size: 1rem;
    }

    .card {
      padding: 20px;
      max-height: 60vh;
    }

    .nav-arrow {
      width: 40px;
      height: 40px;
      font-size: 1rem;
    }

    .nav-prev {
      left: 10px;
    }

    .nav-next {
      right: 10px;
    }

    .timeline::before {
      left: 20px;
    }

    .timeline-item {
      padding-left: 50px;
    }

    .timeline-dot {
      left: 11px;
    }

    .activities-grid {
      grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    }

    .showcase-header h2 {
      font-size: 2rem;
    }

    .showcase-header p {
      font-size: 1rem;
    }

    .skills-cards-container {
      grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
    }
  }

  @media screen and (max-width: 480px) {
    .slide-title {
      font-size: 1.8rem;
    }

    .slide-subtitle {
      font-size: 0.9rem;
    }

    .card {
      padding: 15px;
    }

    .profile-img {
      width: 120px;
      height: 120px;
    }

    .profile-name {
      font-size: 1.6rem;
    }

    .profile-title {
      font-size: 1rem;
    }

    .section-subtitle {
      font-size: 1.3rem;
    }

    .activities-grid {
      grid-template-columns: 1fr 1fr;
    }

    .nav-dots {
      bottom: 20px;
    }

    .skills-showcase {
      padding: 60px 15px;
    }

    .chat-bubble {
      width: 50px;
      height: 50px;
      right: 20px;
      bottom: 20px;
    }

    .chat-content {
      width: 260px;
      right: 20px;
    }
  }
</style>
