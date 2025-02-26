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

  // Define the scroll threshold and animation flag
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
        // We've reached the end of the carousel, animate to skills showcase
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

  onMount(() => {
    if (carouselContainer) {
      window.addEventListener("wheel", handleWheel, { passive: false });
      window.addEventListener("touchstart", handleTouchStart, {
        passive: true,
      });
      window.addEventListener("touchmove", handleTouchMove, { passive: false });
      window.addEventListener("resize", checkViewport);
      document.body.style.overflow = "hidden";

      // Get reference to skills showcase section
      skillsShowcaseSection = document.querySelector(
        ".skills-showcase"
      ) as HTMLElement;

      checkViewport();
      animateContent();

      function updateVhVariable() {
        let vh = window.innerHeight * 0.01;
        document.documentElement.style.setProperty("--vh", `${vh}px`);
      }

      updateVhVariable();

      window.addEventListener("resize", updateVhVariable);

      // Add scroll event listener to handle returning to carousel
      window.addEventListener("scroll", () => {
        if (window.scrollY < 50 && !carouselActive) {
          carouselActive = true;
          document.body.style.overflow = "hidden";
        }
      });

      return () => {
        window.removeEventListener("wheel", handleWheel);
        window.removeEventListener("touchstart", handleTouchStart);
        window.removeEventListener("touchmove", handleTouchMove);
        window.removeEventListener("resize", checkViewport);
        window.removeEventListener("resize", updateVhVariable);
        window.removeEventListener("scroll", () => {});
        document.body.style.overflow = "auto";
      };
    } else {
      window.addEventListener("resize", initializeSkillCards);
      if (skillsShowcaseVisible) {
        initializeSkillCards();
      }
    }
  });

  function initializeSkillCards() {
    const skillCards = document.querySelectorAll(".skill-card");
    const container = document.querySelector(
      ".skills-cards-container"
    ) as HTMLElement;

    skillCards.forEach((card) => {
      const cardElement = card as HTMLElement;
      const randomRotate = Math.random() * 8 - 4;
      const randomOffset = Math.random() * 20 - 10;

      cardElement.style.transform = `rotate(${randomRotate}deg) translate(${randomOffset}px, ${randomOffset}px)`;
    });
  }

  $: if (skillsShowcaseVisible) {
    initializeSkillCards();
  }

  $: if (typeof activeSlide !== "undefined") {
    animateContent();
  }
  $: if (skillsShowcaseVisible) {
    setTimeout(() => {
      initializeSkillCards();
      window.dispatchEvent(new Event("resize"));
    }, 300);
  }
</script>

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
  :global(body) {
    margin: 0;
    padding: 0;
    font-family: "Poppins", "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    overflow-x: hidden;
    --primary: #5d48e3;
    --secondary: #20c997;
    --accent: #e74694;
    --highlight: #23ddaa;
    --dark: #121212;
    --dark-secondary: #1e1e24;
    --dark-tertiary: #2d2d35;
    --text: #e1e1e6;
    --text-secondary: #a0a0a8;
    --card-bg: rgba(30, 30, 36, 0.9);
    --gradient: linear-gradient(135deg, #2d2d40, #181825);
    --shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    --vh: 1vh;
    --safe-area-inset-bottom: env(safe-area-inset-bottom, 0px);
  }

  /* Carousel Styles */
  .carousel {
    width: 100%;
    height: 100vh;
    height: calc(var(--vh, 1vh) * 100);
    position: relative;
    overflow: hidden;
    background-color: var(--dark);
  }

  .slides {
    display: flex;
    height: 100%;
    transition: transform 0.7s cubic-bezier(0.645, 0.045, 0.355, 1);
  }

  .slide {
    flex: 0 0 100%;
    height: 100vh;
    height: calc(var(--vh, 1vh) * 100);
    position: relative;
    overflow: hidden;
  }

  .slide-bg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: var(--gradient);
    z-index: -1;
    overflow: hidden;
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
      30px 30px,
      50px 50px;
    background-position:
      0 0,
      15px 15px;
  }

  .slide-content {
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 2rem;
    color: var(--text);
    text-align: center;

    padding-bottom: calc(2rem + var(--safe-area-inset-bottom) + 30px);
  }

  .slide-title {
    font-size: 3rem;
    margin-bottom: 0.5rem;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.8s forwards;
    font-weight: 700;

    /* Gradient as text color */
    background-image: linear-gradient(to right, var(--primary), var(--accent));
    -webkit-background-clip: text;
    color: transparent;
  }

  .slide-subtitle {
    font-size: 1.5rem;
    margin-bottom: 2rem;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.8s 0.2s forwards;
    color: var(--secondary);
    font-weight: 400;
  }

  .card {
    background: var(--card-bg);
    padding: 2rem;
    border-radius: 15px;
    box-shadow: var(--shadow);
    max-width: 850px;
    width: 90%;
    max-height: 70vh;
    max-height: calc(var(--vh, 2vh) * 70);
    color: var(--text);
    transition: all 0.3s ease;
    position: relative;
    overflow-y: auto;
    scrollbar-width: thin;
    scrollbar-color: var(--primary) var(--dark-tertiary);
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.8s 0.4s forwards;
    border: 1px solid rgba(255, 255, 255, 0.05);
    bottom: 5px;
  }

  /* Styling for scrollbars */
  .card::-webkit-scrollbar {
    width: 8px;
  }

  .card::-webkit-scrollbar-track {
    background: var(--dark-tertiary);
    border-radius: 10px;
  }

  .card::-webkit-scrollbar-thumb {
    background-color: var(--primary);
    border-radius: 10px;
  }

  .card:hover {
    transform: translateY(-5px);
    box-shadow: 0 15px 40px rgba(0, 0, 0, 0.3);
    border-color: rgba(255, 255, 255, 0.1);
  }

  .profile {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
  }

  .profile-img {
    width: 180px;
    height: 180px;
    border-radius: 50%;
    margin-bottom: 1.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 5px solid var(--dark-tertiary);
    box-shadow: var(--shadow);
    position: relative;
    overflow: hidden;
    cursor: pointer;
    transition: all 0.3s ease;
  }

  .profile-img img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    transition: transform 0.5s ease;
  }

  .profile-img:hover img {
    transform: scale(1.1);
  }

  .profile-social-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(93, 72, 227, 0.7);
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  .profile-img:hover .profile-social-overlay {
    opacity: 1;
  }

  .profile-social-overlay i {
    font-size: 50px;
    color: white;
  }

  .profile-info {
    width: 100%;
    margin-bottom: 1.5rem;
  }

  .profile-name {
    font-size: 2.3rem;
    margin-bottom: 0.5rem;
    color: var(--text);
    cursor: pointer;
    display: inline-block;
    transition: color 0.3s ease;
  }

  .profile-name:hover {
    color: var(--primary);
  }

  .profile-title {
    font-size: 1.2rem;
    color: var(--secondary);
    margin-bottom: 2rem;
  }
  button {
    background: none;
    border: none;
    padding: 0;
    font: inherit;
    cursor: pointer;
    outline: inherit;
  }

  .story-container {
    opacity: 0;
    transform: translateY(20px);
    transition: all 0.6s ease;
    text-align: left;
    max-width: 650px;
    margin: 0 auto;
  }

  .story-container.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .story-text {
    margin-bottom: 1.5rem;
    line-height: 1.7;
    color: var(--text-secondary);
  }

  .contact-button {
    margin-top: 2rem;
    text-align: center;
  }

  .contact-button a {
    background: var(--primary);
    color: white;
    padding: 0.8rem 2rem;
    border-radius: 30px;
    text-decoration: none;
    display: inline-block;
    font-weight: 600;
    transition: all 0.3s ease;
    box-shadow: 0 5px 15px rgba(93, 72, 227, 0.3);
  }

  .contact-button a:hover {
    background: #4a3ac8;
    transform: translateY(-3px);
    box-shadow: 0 8px 20px rgba(93, 72, 227, 0.4);
  }

  /* Skills Section */
  .skills-container {
    width: 100%;
  }

  .skill-category {
    margin-bottom: 1.8rem;
    opacity: 0;
    transform: translateY(20px);
  }

  .skill-category.visible {
    animation: fadeUp 0.5s forwards;
  }

  .skill-category-title {
    font-size: 1.2rem;
    margin-bottom: 1rem;
    color: var(--accent);
    border-bottom: 2px solid rgba(32, 201, 151, 0.3);
    padding-bottom: 0.5rem;
  }

  .skill-items {
    display: flex;
    flex-wrap: wrap;
    gap: 0.7rem;
  }

  .skill-item {
    background: var(--dark-tertiary);
    color: var(--text);
    padding: 0.5rem 1rem;
    border-radius: 20px;
    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.2);
    transition: all 0.3s ease;
    border: 1px solid rgba(255, 255, 255, 0.05);
  }

  .skill-item:hover {
    transform: translateY(-3px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.25);
    background: var(--primary);
    color: white;
    border-color: transparent;
  }

  .timeline {
    position: relative;
    margin: 2rem 0;
    padding: 0 1rem;
  }

  .timeline::before {
    content: "";
    position: absolute;
    left: 0;
    top: 0;
    width: 4px;
    height: 100%;
    background: var(--primary);
    border-radius: 4px;
    opacity: 0.5;
  }

  .timeline-item {
    position: relative;
    padding-left: 2rem;
    margin-bottom: 2.5rem;
    opacity: 0;
    transform: translateX(-20px);
  }

  .timeline-item.visible {
    animation: fadeRight 0.5s forwards;
  }

  .timeline-dot {
    position: absolute;
    left: -8px;
    top: 5px;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: var(--accent);
    border: 4px solid var(--dark-tertiary);
    box-shadow: var(--shadow);
  }

  .timeline-period {
    font-weight: bold;
    color: var(--accent);
    margin-bottom: 0.3rem;
    font-size: 1.1rem;
  }

  .timeline-title {
    font-weight: 600;
    color: var(--text);
    margin-bottom: 0.5rem;
    font-size: 1.3rem;
  }

  .timeline-description {
    color: var(--text-secondary);
    line-height: 1.6;
  }

  /* Activities Section */
  .section-subtitle {
    font-size: 1.4rem;
    margin-bottom: 1.5rem;
    color: var(--secondary);
    padding-bottom: 0.5rem;
    border-bottom: 2px solid rgba(32, 201, 151, 0.3);
    text-align: left;
    width: 100%;
  }

  .activities-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 1.5rem;
    margin-bottom: 2.5rem;
  }

  .activity-item {
    background: var(--dark-tertiary);
    padding: 1.5rem;
    border-radius: 10px;
    text-align: center;
    transition: all 0.3s ease;
    opacity: 0;
    transform: translateY(20px);
    border: 1px solid rgba(255, 255, 255, 0.05);
  }

  .activity-item.visible {
    animation: fadeUp 0.5s forwards;
  }

  .activity-item:hover {
    transform: translateY(-5px);
    box-shadow: var(--shadow);
    border-color: rgba(255, 255, 255, 0.1);
  }

  .activity-icon {
    font-size: 2rem;
    color: var(--secondary);
    margin-bottom: 1rem;
  }

  .activity-year {
    font-size: 0.9rem;
    color: var(--accent);
    margin-bottom: 0.5rem;
    font-weight: 600;
  }

  .activity-title {
    color: var(--text);
    font-weight: 500;
  }

  .qualifications-list {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 1rem;
    margin-top: 1.5rem;
  }

  .qualification-item {
    margin-bottom: 0.5rem;
    padding-left: 1.5rem;
    position: relative;
    transition: transform 0.3s ease;
  }

  .qualification-item:hover {
    transform: translateX(5px);
  }

  .qualification-item::before {
    content: "✓";
    position: absolute;
    left: 0;
    color: var(--highlight);
    font-weight: bold;
  }

  /* Projects Section */
  .projects-container {
    width: 100%;
  }

  .project-card {
    background: var(--dark-tertiary);
    border-radius: 12px;
    padding: 1.5rem;
    margin-bottom: 1.5rem;
    box-shadow: var(--shadow);
    transition: all 0.3s ease;
    opacity: 0;
    transform: translateY(20px);
    border: 1px solid rgba(255, 255, 255, 0.05);
  }

  .project-card.visible {
    animation: fadeUp 0.5s forwards;
  }

  .project-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.3);
    border-color: rgba(255, 255, 255, 0.1);
  }

  .project-title {
    font-size: 1.5rem;
    margin-bottom: 1rem;
    color: var(--text);
  }

  .project-description {
    margin-bottom: 1.5rem;
    line-height: 1.7;
    color: var(--text-secondary);
  }

  .project-tech {
    display: flex;
    flex-wrap: wrap;
    gap: 0.7rem;
    margin-bottom: 1.5rem;
  }

  .tech-badge {
    background: var(--primary);
    color: white;
    padding: 0.4rem 0.8rem;
    border-radius: 15px;
    font-size: 0.9rem;
    font-weight: 500;
  }

  .project-links {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
  }

  .project-link {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    background: var(--dark);
    color: var(--text);
    padding: 0.6rem 1.2rem;
    border-radius: 20px;
    text-decoration: none;
    transition: all 0.3s ease;
    font-weight: 500;
  }

  .project-link:hover {
    background: var(--accent);
    color: white;
    transform: translateY(-3px);
  }

  /* Navigation controls */
  .nav-dots {
    position: absolute;
    bottom: calc(20px + var(--safe-area-inset-bottom));
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 0.8rem;
    z-index: 10;
  }

  .nav-dot {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.3);
    cursor: pointer;
    transition: all 0.3s ease;
    border: none;
  }

  .nav-dot.active {
    background: var(--primary);
    transform: scale(1.2);
  }

  .nav-arrow {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: rgba(93, 72, 227, 0.2);
    color: white;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.3s ease;
    z-index: 10;
    border: 1px solid rgba(255, 255, 255, 0.1);
  }

  .nav-arrow:hover {
    background: var(--primary);
    transform: translateY(-50%) scale(1.1);
  }

  .nav-prev {
    left: 20px;
  }

  .nav-next {
    right: 20px;
  }

  /* Chat Bubble */
  .chat-bubble {
    position: fixed;
    bottom: calc(20px + var(--safe-area-inset-bottom));
    right: 20px;
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background: var(--primary);
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 5px 20px rgba(93, 72, 227, 0.4);
    cursor: pointer;
    z-index: 100;
    transition: all 0.3s ease;
    border: none;
  }

  .chat-bubble:hover {
    transform: scale(1.1);
    background: #4a3ac8;
  }

  .chat-icon {
    width: 28px;
    height: 28px;
    filter: brightness(0) invert(1);
  }

  .chat-content {
    position: fixed;
    bottom: 8rem;
    right: 2rem;
    width: 300px;
    background: var(--card-bg);
    border-radius: 15px;
    box-shadow: var(--shadow);
    padding: 1.5rem;
    opacity: 0;
    transform: translateY(20px);
    pointer-events: none;
    transition: all 0.3s ease;
    z-index: 100;
    border: 1px solid rgba(255, 255, 255, 0.05);
  }

  .chat-content.open {
    opacity: 1;
    transform: translateY(0);
    pointer-events: all;
  }

  .chat-header {
    font-size: 1.5rem;
    margin-bottom: 1rem;
    color: var(--text);
    font-weight: 600;
  }

  .chat-info {
    margin-bottom: 1.5rem;
    color: var(--text-secondary);
  }

  .chat-info p {
    margin-bottom: 0.5rem;
  }

  .chat-action {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    background: var(--primary);
    color: white;
    padding: 0.8rem 1.5rem;
    border-radius: 30px;
    text-decoration: none;
    font-weight: 600;
    transition: all 0.3s ease;
    box-shadow: 0 5px 15px rgba(93, 72, 227, 0.3);
  }

  .chat-action:hover {
    background: #4a3ac8;
    transform: translateY(-3px);
    box-shadow: 0 8px 20px rgba(93, 72, 227, 0.4);
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

  @keyframes fadeRight {
    from {
      opacity: 0;
      transform: translateX(-20px);
    }
    to {
      opacity: 1;
      transform: translateX(0);
    }
  }

  /* Media Queries */
  @media (max-width: 768px) {
    .slide-title {
      font-size: 2rem;
    }

    .slide-subtitle {
      font-size: 1.2rem;
    }

    .card {
      padding: 1.5rem;
      width: 95%;
      max-height: 70vh;
      max-height: calc(var(--vh, 1vh) * 100);
    }

    .profile-img {
      width: 140px;
      height: 140px;
    }

    .profile-name {
      font-size: 1.8rem;
    }

    .nav-arrow {
      width: 40px;
      height: 40px;
    }

    .activities-grid {
      grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
      gap: 1rem;
    }

    .activity-item {
      padding: 1rem;
    }

    .timeline-item {
      padding-left: 1.5rem;
    }
  }

  @media (max-width: 480px) {
    .slide-title {
      font-size: 1.8rem;
    }

    .slide-subtitle {
      font-size: 1rem;
    }

    .card {
      padding: 1rem;
    }

    .nav-arrow {
      display: none;
    }

    .activities-grid {
      grid-template-columns: 1fr 1fr;
    }
  }

  .skills-showcase {
    position: relative;
    min-height: 100vh;
    padding: 4rem 2rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }

  .skills-showcase-background {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(
      45deg,
      var(--dark) 20%,
      var(--dark-secondary) 80%
    );
    z-index: 0;
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
    animation:
      bit-fade 1.5s ease-in-out infinite,
      glitch 0.3s infinite;
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

  /* Mobile Optimierungen */
  @media (max-width: 768px) {
    .binary-rain {
      mix-blend-mode: soft-light;
    }

    .bit {
      font-size: 1rem;
      opacity: 0.6;
    }

    .column {
      animation-duration: calc(var(--speed) * 1.2);
    }
  }

  .showcase-header {
    position: relative;
    z-index: 2;
    text-align: center;
    margin-bottom: 3rem;
    color: var(--text);
  }

  .showcase-header h2 {
    font-size: 2.5rem;
    margin-bottom: 0.5rem;
    background: linear-gradient(to right, var(--primary), var(--accent));
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
  }

  .showcase-header p {
    font-size: 1.2rem;
    color: var(--text-secondary);
  }

  .showcase-header h2 {
    font-size: 2.5rem;
    margin-bottom: 0.5rem;
    background: linear-gradient(to right, var(--primary), var(--accent));
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
  }

  .showcase-header p {
    font-size: 1.2rem;
    color: var(--text-secondary);
  }

  @keyframes flowPath {
    from {
      stroke-dashoffset: 1000;
    }
    to {
      stroke-dashoffset: 0;
    }
  }
  .skills-cards-container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 5rem;
    padding: 2rem;
    max-width: 1200px;
    width: 100%;
    margin: 0 auto;
  }
  .skill-card {
    width: 100%;
    height: 250px;
    perspective: 1000px;
    border-radius: 20px;
    box-shadow: var(--shadow);
    cursor: pointer;
    transition: transform 0.3s ease;
    background: var(--dark-tertiary);
  }

  .skill-card:hover {
    transform: rotate(0deg) scale(1.05) !important;
    z-index: 10;
    box-shadow: 0 15px 35px rgba(0, 0, 0, 0.4);
  }

  .skill-card-inner {
    position: relative;
    width: 100%;
    height: 100%;
    transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
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
    -webkit-backface-visibility: hidden;
    backface-visibility: hidden;
    border-radius: 20px;
    overflow: hidden;
  }

  .skill-card-front {
    background: var(--dark-tertiary);
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .skill-card-front::after {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(
      135deg,
      rgba(93, 72, 227, 0.2),
      rgba(231, 70, 148, 0.2)
    );
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  .skill-card:hover .skill-card-front::after {
    opacity: 1;
  }

  .skill-card-front img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    transition: all 0.5s ease;
  }

  .skill-card:hover .skill-card-front img {
    transform: scale(1.1);
  }

  .card-icon {
    position: relative;
    width: 100%;
    height: 100%;
  }

  .card-icon i {
    position: absolute;
    bottom: 15px;
    right: 15px;
    font-size: 2rem;
    color: rgba(255, 255, 255, 0.8);
    z-index: 2;
    filter: drop-shadow(0 0 8px rgba(0, 0, 0, 0.5));
  }

  .skill-card-back {
    background: linear-gradient(135deg, var(--primary), var(--accent));
    color: white;
    transform: rotateY(180deg);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 1.5rem;
    backface-visibility: hidden;
    position: relative;
    z-index: 2;
  }

  .skill-card-back::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><rect fill="none" width="100" height="100"/><path d="M0,0 L100,100 M0,50 L50,100 M50,0 L100,50" stroke="rgba(255,255,255,0.1)" stroke-width="1"/></svg>');
    opacity: 0.3;
  }

  .skill-card-back h3 {
    font-size: 1.8rem;
    margin-bottom: 0.5rem;
    font-weight: 700;
    text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
  }

  .skill-card-back p {
    font-size: 1.1rem;
    opacity: 0.9;
    font-weight: 500;
    text-align: center;
    line-height: 1.4;
  }

  /* Add subtle glow effect */
  @keyframes glow {
    0%,
    100% {
      box-shadow: 0 0 15px rgba(93, 72, 227, 0.3);
    }
    50% {
      box-shadow: 0 0 25px rgba(231, 70, 148, 0.5);
    }
  }

  .skill-card {
    animation: glow 5s ease-in-out infinite;
    animation-delay: calc(var(--order) * 1s);
  }

  .skill-card:nth-child(1) {
    --order: 0;
  }
  .skill-card:nth-child(2) {
    --order: 1;
  }
  .skill-card:nth-child(3) {
    --order: 2;
  }
  .skill-card:nth-child(4) {
    --order: 3;
  }
  .skill-card:nth-child(5) {
    --order: 4;
  }
  .skill-card:nth-child(6) {
    --order: 5;
  }

  @media (max-width: 992px) {
    .skills-cards-container {
      grid-template-columns: repeat(2, 1fr);
      gap: 2rem;
    }
  }

  @media (max-width: 768px) {
    .showcase-header h2 {
      font-size: 2rem;
    }

    .showcase-header p {
      font-size: 1rem;
    }

    .skills-cards-container {
      gap: 1.5rem;
    }

    .skill-card {
      height: 220px;
    }

    .skill-card-back h3 {
      font-size: 1.5rem;
    }

    .skill-card-back p {
      font-size: 1rem;
    }
  }

  .skills-showcase {
    padding: 4rem 1rem;
  }

  .showcase-header h2 {
    font-size: 2rem;
  }

  .showcase-header p {
    font-size: 1rem;
  }

  .connections {
    stroke-width: 0.5;
  }

  .particle {
    display: none;
  }
</style>
