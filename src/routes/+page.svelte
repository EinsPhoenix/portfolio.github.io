<script lang="ts">
  import { onMount } from "svelte";
  import { base } from "$app/paths";

  // Carousel control
  let activeSlide = 0;
  let carouselContainer: HTMLElement;
  let isScrolling = false;
  let carouselActive = true;

  // Chat bubble control
  let isChatOpen = false;

  // Animations
  let skillsVisible = false;
  let storyVisible = false;

  // Slides for the carousel
  const slides = [
    {
      title: "Noah Kirsch",
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
    {
      year: "2011",
      title: "Scout Martin von Tours",
      icon: "campground",
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

  // Handler for mouse wheel
  const handleWheel = (e: WheelEvent) => {
    if (carouselActive) {
      e.preventDefault();
    }

    if (isScrolling) return;
    isScrolling = true;

    const delta = e.deltaY;

    if (delta > 0) {
      // Scroll down
      if (activeSlide < slides.length - 1) {
        activeSlide++;
      } else if (carouselActive) {
        carouselActive = false;
        window.scrollTo({ top: window.innerHeight, behavior: "smooth" });
        document.body.style.overflow = "auto";
      }
    } else if (delta < 0) {
      // Scroll up
      if (window.scrollY > 0 && window.scrollY < window.innerHeight) {
        window.scrollTo({ top: 0, behavior: "smooth" });
        carouselActive = true;
        document.body.style.overflow = "hidden";
      } else if (carouselActive && activeSlide > 0) {
        activeSlide--;
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

  onMount(() => {
    if (carouselContainer) {
      window.addEventListener("wheel", handleWheel, { passive: false });
      document.body.style.overflow = "hidden";

      animateContent();

      return () => {
        window.removeEventListener("wheel", handleWheel);
        document.body.style.overflow = "auto";
      };
    }
  });

  $: if (typeof activeSlide !== "undefined") {
    animateContent();
  }
</script>

<!-- Main carousel -->
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
  }

  /* Carousel Styles */
  .carousel {
    width: 100%;
    height: 100vh;
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
  }

  .slide-title {
    font-size: 3rem;
    margin-bottom: 0.5rem;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.8s forwards;
    color: var(--text);
    font-weight: 700;
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
    max-width: 800px;
    width: 90%;
    color: var(--text);
    transition: all 0.3s ease;
    position: relative;
    overflow: hidden;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.8s 0.4s forwards;
    border: 1px solid rgba(255, 255, 255, 0.05);
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
    color: var(--secondary);
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
    color: var(--secondary);
    margin-bottom: 0.5rem;
  }

  .timeline-title {
    font-size: 1.3rem;
    margin-bottom: 0.5rem;
    color: var(--text);
  }

  .timeline-description {
    color: var(--text-secondary);
    line-height: 1.6;
  }

  .section-subtitle {
    color: var(--secondary);
    margin: 1.5rem 0;
    font-size: 1.5rem;
    position: relative;
    display: inline-block;
  }

  .section-subtitle::after {
    content: "";
    position: absolute;
    bottom: -10px;
    left: 0;
    width: 60px;
    height: 3px;
    background: var(--secondary);
    border-radius: 3px;
  }

  .activities-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
    gap: 1.2rem;
    margin-bottom: 3rem;
  }

  .activity-item {
    background: var(--dark-tertiary);
    border-radius: 10px;
    padding: 1.2rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    transition: all 0.3s ease;
    opacity: 0;
    transform: scale(0.9);
    border: 1px solid rgba(255, 255, 255, 0.05);
  }

  .activity-item.visible {
    animation: popIn 0.5s forwards;
  }

  .activity-item:hover {
    transform: translateY(-5px);
    box-shadow: var(--shadow);
    background: var(--primary);
    border-color: transparent;
  }

  .activity-icon {
    font-size: 2rem;
    margin-bottom: 1rem;
    color: var(--secondary);
    transition: color 0.3s ease;
  }

  .activity-item:hover .activity-icon {
    color: white;
  }

  .activity-year {
    font-weight: 600;
    margin-bottom: 0.5rem;
    color: var(--text-secondary);
    transition: color 0.3s ease;
  }

  .activity-item:hover .activity-year {
    color: rgba(255, 255, 255, 0.9);
  }

  .activity-title {
    line-height: 1.4;
    transition: color 0.3s ease;
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

  /* Projects section */
  .projects-container {
    display: grid;
    grid-template-columns: 1fr;
    gap: 2rem;
  }

  .project-card {
    background: var(--dark-tertiary);
    border-radius: 12px;
    overflow: hidden;
    box-shadow: var(--shadow);
    transition: all 0.3s ease;
    border: 1px solid rgba(255, 255, 255, 0.05);
    opacity: 0;
    transform: translateY(20px);
  }

  .project-card.visible {
    animation: fadeUp 0.6s forwards;
  }

  .project-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 15px 40px rgba(0, 0, 0, 0.3);
    border-color: rgba(255, 255, 255, 0.1);
  }

  .project-content {
    padding: 1.5rem;
  }

  .project-title {
    font-size: 1.5rem;
    margin-bottom: 1rem;
    color: var(--text);
  }

  .project-description {
    margin-bottom: 1.5rem;
    line-height: 1.6;
    color: var(--text-secondary);
  }

  .project-tech {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    margin-bottom: 1.5rem;
  }

  /* Fortsetzung der tech-badge styles */
  .tech-badge {
    background: rgba(93, 72, 227, 0.2);
    color: var(--primary);
    padding: 0.3rem 0.8rem;
    border-radius: 20px;
    font-size: 0.9rem;
    border: 1px solid rgba(93, 72, 227, 0.3);
    transition: all 0.3s ease;
  }

  .tech-badge:hover {
    background: var(--primary);
    color: white;
    border-color: transparent;
  }

  .project-links {
    display: flex;
    gap: 1rem;
    flex-wrap: wrap;
  }

  .project-link {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    background: var(--dark);
    color: var(--text);
    padding: 0.5rem 1rem;
    border-radius: 20px;
    text-decoration: none;
    font-size: 0.9rem;
    transition: all 0.3s ease;
    border: 1px solid rgba(255, 255, 255, 0.1);
  }

  .project-link:hover {
    background: var(--primary);
    color: white;
    transform: translateY(-2px);
    border-color: transparent;
  }

  /* Navigation Dots */
  .nav-dots {
    position: absolute;
    bottom: 2rem;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 0.5rem;
  }

  .nav-dot {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.3);
    cursor: pointer;
    transition: all 0.3s ease;
  }

  .nav-dot.active {
    background: var(--primary);
    transform: scale(1.2);
  }

  .nav-dot:hover {
    background: var(--secondary);
  }

  /* Navigation Arrows */
  .nav-arrow {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background: rgba(30, 30, 36, 0.7);
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    color: var(--text);
    font-size: 1.2rem;
    transition: all 0.3s ease;
    z-index: 10;
    border: 1px solid rgba(255, 255, 255, 0.1);
  }

  .nav-prev {
    left: 1.5rem;
  }

  .nav-next {
    right: 1.5rem;
  }

  .nav-arrow:hover {
    background: var(--primary);
    color: white;
    transform: translateY(-50%) scale(1.1);
    border-color: transparent;
  }

  /* Chat Bubble */
  .chat-bubble {
    position: fixed;
    bottom: 2rem;
    right: 2rem;
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background: var(--primary);
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    box-shadow: 0 5px 15px rgba(93, 72, 227, 0.3);
    z-index: 100;
    transition: all 0.3s ease;
  }

  .chat-bubble:hover {
    transform: scale(1.1);
    background: #4a3ac8;
  }

  .chat-icon {
    width: 30px;
    height: 30px;
  }

  /* Chat Content */
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

  @keyframes popIn {
    from {
      opacity: 0;
      transform: scale(0.9);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }

  /* Responsive Design */
  @media (max-width: 768px) {
    .slide-title {
      font-size: 2.2rem;
    }

    .slide-subtitle {
      font-size: 1.2rem;
    }

    .card {
      padding: 1.5rem;
      width: 95%;
    }

    .profile-img {
      width: 150px;
      height: 150px;
    }

    .activities-grid {
      grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    }

    .nav-arrow {
      width: 40px;
      height: 40px;
    }

    .nav-prev {
      left: 1rem;
    }

    .nav-next {
      right: 1rem;
    }
  }

  @media (max-width: 480px) {
    .slide-title {
      font-size: 1.8rem;
    }

    .slide-subtitle {
      font-size: 1rem;
    }

    .profile-name {
      font-size: 1.8rem;
    }

    .profile-title {
      font-size: 1rem;
    }

    .activities-grid {
      grid-template-columns: 1fr 1fr;
    }

    .chat-bubble {
      width: 50px;
      height: 50px;
      bottom: 1.5rem;
      right: 1.5rem;
    }

    .chat-content {
      width: 250px;
      right: 1.5rem;
    }
  }
</style>
