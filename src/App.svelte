<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  
  gsap.registerPlugin(ScrollTrigger);

  let activeSection = "home";
  let scrollProgress = 0;
  let mouseX = 0;
  let mouseY = 0;
  let mobileMenuOpen = false;
  let formData = {
    name: "",
    email: "",
    message: ""
  };
  let formStatus = {
    submitting: false,
    success: false,
    error: false,
    message: ""
  };

  const skills = [
    { name: "JavaScript", level: 90, icon: "fab fa-js-square" },
    { name: "Svelte", level: 85, icon: "fab fa-svelte" },
    { name: "HTML/CSS", level: 95, icon: "fab fa-html5" },
    { name: "TailwindCSS", level: 88, icon: "fab fa-css3-alt" },
    { name: "C#", level: 80, icon: "fab fa-microsoft" },
    { name: "Git", level: 85, icon: "fab fa-git-alt" }
  ];

  function scrollToSection(section) {
    activeSection = section;
    const el = document.getElementById(section);
    if (el) {
      const headerOffset = 80;
      const elementPosition = el.getBoundingClientRect().top;
      const offsetPosition = elementPosition + window.pageYOffset - headerOffset;
      
      window.scrollTo({
        top: offsetPosition,
        behavior: "smooth"
      });
    }
  }

  function handleMouseMove(e) {
    mouseX = e.clientX / window.innerWidth;
    mouseY = e.clientY / window.innerHeight;
  }

  async function handleFormSubmit(e) {
    e.preventDefault();
    formStatus.submitting = true;
    formStatus.success = false;
    formStatus.error = false;
    formStatus.message = "";

    try {
      // Using Web3Forms - free form service (no signup required)
      const response = await fetch("https://api.web3forms.com/submit", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          access_key: "YOUR_ACCESS_KEY", // Replace with your Web3Forms access key from https://web3forms.com
          subject: "New Contact Form Submission from Portfolio",
          from_name: formData.name,
          email: formData.email,
          message: formData.message,
          to_email: "ibrahemkhalilv@gmail.com"
        })
      });

      const data = await response.json();

      if (data.success) {
        formStatus.success = true;
        formStatus.message = "Thank you! Your message has been sent successfully.";
        formData = { name: "", email: "", message: "" };
        
        // Reset success message after 5 seconds
        setTimeout(() => {
          formStatus.success = false;
          formStatus.message = "";
        }, 5000);
      } else {
        throw new Error(data.message || "Failed to send message");
      }
    } catch (error) {
      formStatus.error = true;
      formStatus.message = "Sorry, there was an error sending your message. Please try again or email me directly at ibrahemkhalilv@gmail.com";
    } finally {
      formStatus.submitting = false;
    }
  }

  onMount(() => {
    // Floating logos animation
    gsap.utils.toArray(".floating-logo").forEach((logo, i) => {
      gsap.to(logo, {
        duration: 3 + Math.random() * 2,
        repeat: -1,
        yoyo: true,
        x: Math.random() * 20 - 10,
        y: Math.random() * 20 - 10,
        rotate: Math.random() * 15 - 7.5,
        ease: "sine.inOut"
      });
    });

    // Scroll progress
    const updateScrollProgress = () => {
      const windowHeight = window.innerHeight;
      const documentHeight = document.documentElement.scrollHeight;
      const scrollTop = window.scrollY;
      scrollProgress = (scrollTop / (documentHeight - windowHeight)) * 100;
    };
    window.addEventListener("scroll", updateScrollProgress);

    // Mouse tracking for parallax
    window.addEventListener("mousemove", handleMouseMove);

    // Parallax effect for hero image
    const heroImg = document.querySelector(".hero-img");
    let parallaxAnimation;
    const updateParallax = () => {
      if (heroImg) {
        if (parallaxAnimation) parallaxAnimation.kill();
        parallaxAnimation = gsap.to(heroImg, {
          y: mouseY * 20,
          x: mouseX * 20,
          ease: "power1.out",
          duration: 0.5
        });
      }
    };
    if (heroImg) {
      window.addEventListener("mousemove", updateParallax);
    }

    // Section scroll detection
    const sections = ["home", "about", "skills", "projects", "contact"];
    const handleScroll = () => {
      const scrollPos = window.scrollY + 200;
      for (const section of sections) {
        const el = document.getElementById(section);
        if (el && scrollPos >= el.offsetTop) {
          activeSection = section;
        }
      }
    };
    window.addEventListener("scroll", handleScroll);

    // Fade in animations for sections
    gsap.utils.toArray("section").forEach((section) => {
      gsap.from(section, {
        opacity: 0,
        y: 50,
        duration: 1,
        scrollTrigger: {
          trigger: section,
          start: "top 80%",
          end: "bottom 20%",
          toggleActions: "play none none none"
        },
        ease: "power2.out"
      });
    });

    // Animate skill bars
    gsap.utils.toArray(".skill-bar").forEach((bar) => {
      ScrollTrigger.create({
        trigger: bar,
        start: "top 80%",
        onEnter: () => {
          const width = bar.dataset.width;
          gsap.to(bar, {
            width: `${width}%`,
            duration: 1.5,
            ease: "power2.out"
          });
        }
      });
    });

    return () => {
      window.removeEventListener("scroll", handleScroll);
      window.removeEventListener("scroll", updateScrollProgress);
      window.removeEventListener("mousemove", handleMouseMove);
      if (heroImg) {
        window.removeEventListener("mousemove", updateParallax);
      }
      if (parallaxAnimation) parallaxAnimation.kill();
    };
  });
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap');
  
  * {
    font-family: 'Inter', sans-serif;
  }

  @keyframes logoGlow {
    0%, 100% { 
      filter: drop-shadow(0 0 10px rgba(99, 102, 241, 0.5)) 
              drop-shadow(0 0 20px rgba(99, 102, 241, 0.3)); 
    }
    50% { 
      filter: drop-shadow(0 0 20px rgba(139, 92, 246, 0.8)) 
              drop-shadow(0 0 30px rgba(99, 102, 241, 0.5)); 
    }
  }

  @keyframes gradient {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }

  @keyframes float {
    0%, 100% { transform: translateY(0px); }
    50% { transform: translateY(-20px); }
  }

  @keyframes shimmer {
    0% { background-position: -1000px 0; }
    100% { background-position: 1000px 0; }
  }

  .glow-logo {
    animation: logoGlow 3s infinite ease-in-out;
  }

  .gradient-text {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 50%, #f093fb 100%);
    background-size: 200% 200%;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    animation: gradient 3s ease infinite;
  }

  .glass {
    background: rgba(255, 255, 255, 0.05);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
  }

  .glass-strong {
    background: rgba(255, 255, 255, 0.08);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border: 1px solid rgba(255, 255, 255, 0.15);
  }

  .name-hover {
    transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
    display: inline-block;
    transform-origin: center;
    cursor: pointer;
  }

  .name-hover:hover {
    transform: scale(1.1) translateY(-5px);
    text-shadow: 0 10px 30px rgba(139, 92, 246, 0.5);
  }

  .hero-img {
    transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
    cursor: pointer;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  }

  .hero-img:hover {
    transform: scale(1.05) rotate(2deg);
    box-shadow: 0 30px 80px rgba(139, 92, 246, 0.4);
  }

  .floating-logo {
    position: absolute;
    width: 150px;
    height: 150px;
    pointer-events: none;
    opacity: 0.5;
    filter: blur(0.5px);
  }

  .skill-bar {
    width: 0%;
    transition: width 1.5s ease-out;
  }

  .project-card {
    position: relative;
    overflow: hidden;
  }

  .project-card::before {
    content: '';
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.1), transparent);
    transition: left 0.5s;
  }

  .project-card:hover::before {
    left: 100%;
  }

  .scroll-progress {
    position: fixed;
    top: 0;
    left: 0;
    height: 3px;
    background: linear-gradient(90deg, #667eea, #764ba2, #f093fb);
    z-index: 9999;
    transition: width 0.1s ease-out;
  }

  .particle {
    position: absolute;
    width: 4px;
    height: 4px;
    background: rgba(139, 92, 246, 0.5);
    border-radius: 50%;
    pointer-events: none;
    animation: float 6s infinite ease-in-out;
  }

  .btn-premium {
    position: relative;
    overflow: hidden;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    transition: all 0.3s ease;
  }

  .btn-premium::before {
    content: '';
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
    transition: left 0.5s;
  }

  .btn-premium:hover::before {
    left: 100%;
  }

  .btn-premium:hover {
    transform: translateY(-2px);
  }
  
  /* Remove ALL shadows from header - AGGRESSIVE */
  header * {
    box-shadow: none !important;
    text-shadow: none !important;
    filter: none !important;
    --tw-shadow: 0 0 #0000 !important;
    --tw-shadow-colored: 0 0 #0000 !important;
  }
  
  header button,
  header nav button,
  header a,
  header span {
    box-shadow: none !important;
    text-shadow: none !important;
    filter: none !important;
    --tw-shadow: 0 0 #0000 !important;
    --tw-shadow-colored: 0 0 #0000 !important;
  }
  
  header button:hover,
  header button:focus,
  header button:active,
  header nav button:hover,
  header nav button:focus,
  header nav button:active,
  header a:hover,
  header a:focus,
  header a:active {
    box-shadow: none !important;
    text-shadow: none !important;
    filter: none !important;
    --tw-shadow: 0 0 #0000 !important;
    --tw-shadow-colored: 0 0 #0000 !important;
  }

  .input-premium {
    background: rgba(255, 255, 255, 0.05);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    transition: all 0.3s ease;
  }

  .input-premium:focus {
    background: rgba(255, 255, 255, 0.08);
    border-color: rgba(139, 92, 246, 0.5);
    box-shadow: 0 0 20px rgba(139, 92, 246, 0.2);
    outline: none;
  }

  .nav-link {
    position: relative;
    transition: all 0.3s ease;
    box-shadow: none !important;
    text-shadow: none !important;
    filter: none !important;
    --tw-shadow: 0 0 #0000 !important;
    --tw-shadow-colored: 0 0 #0000 !important;
  }
  
  .nav-link:hover,
  .nav-link:focus,
  .nav-link:active,
  .nav-link.active {
    box-shadow: none !important;
    text-shadow: none !important;
    filter: none !important;
    --tw-shadow: 0 0 #0000 !important;
    --tw-shadow-colored: 0 0 #0000 !important;
  }

  .nav-link::after {
    content: '';
    position: absolute;
    bottom: -5px;
    left: 50%;
    transform: translateX(-50%) scaleX(0);
    width: 100%;
    height: 2px;
    background: linear-gradient(90deg, #667eea, #764ba2);
    transition: transform 0.3s ease;
  }

  .nav-link:hover::after,
  .nav-link.active::after {
    transform: translateX(-50%) scaleX(1);
  }

  @keyframes slideDown {
    from {
      opacity: 0;
      transform: translateY(-10px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .animate-slideDown {
    animation: slideDown 0.3s ease-out;
  }
</style>

<!-- Scroll Progress Bar -->
<div class="scroll-progress" style="width: {scrollProgress}%"></div>

<main class="min-h-screen bg-gradient-to-br from-[#0a0a1a] via-[#1a0a2e] to-[#0a0a1a] text-white relative overflow-hidden" style="margin: 0 !important; padding: 0 !important;">
  
  <!-- Animated Background Particles -->
  <div class="fixed inset-0 pointer-events-none overflow-hidden">
    {#each Array(20) as _, i}
      <div class="particle" style="left: {Math.random() * 100}%; top: {Math.random() * 100}%; animation-delay: {Math.random() * 6}s; animation-duration: {4 + Math.random() * 4}s;"></div>
    {/each}
  </div>

  <!-- Premium Header -->
  <header class="fixed inset-x-0 top-0 z-50 glass-strong border-b border-white/10 backdrop-blur-xl bg-[rgba(12,14,43,0.8)] w-full" style="margin: 0 !important; padding: 0 !important;">
    <div class="w-full px-4 sm:px-6 lg:px-8 xl:px-12 2xl:px-16">
      <div class="flex items-center justify-between h-20 md:h-24 xl:h-28 2xl:h-32 relative">
        <!-- Left: Logo and Name - Edge of Left -->
        <div class="flex items-center gap-3 md:gap-4 xl:gap-5 2xl:gap-6 flex-shrink-0">
          <button 
            class="flex items-center gap-3 md:gap-4 xl:gap-5 2xl:gap-6 cursor-pointer group bg-transparent border-none outline-none p-2 flex-shrink-0"
            on:click={() => scrollToSection("home")}
            aria-label="Go to home"
          >
            <div class="w-14 h-14 md:w-16 md:h-16 xl:w-20 xl:h-20 2xl:w-24 2xl:h-24 rounded-full overflow-hidden glow-logo ring-2 ring-purple-500/30 group-hover:ring-purple-500/60 transition-all duration-300 group-hover:scale-110 flex-shrink-0">
              <img src="../img/lc-or-cl-bold-monogram-logo-vector-48182063-removebg-preview.png" alt="Logo" class="w-full h-full object-cover" />
            </div>
            <span class="text-lg md:text-xl lg:text-2xl xl:text-3xl 2xl:text-4xl font-bold gradient-text hidden sm:block group-hover:scale-105 transition-transform duration-300 whitespace-nowrap">Ibrahem Khalil</span>
          </button>

          <!-- Mobile Menu Button -->
          <button
            class="md:hidden w-11 h-11 flex flex-col justify-center gap-1.5 glass rounded-lg hover:bg-white/10 transition-all duration-300 flex-shrink-0"
            on:click={() => mobileMenuOpen = !mobileMenuOpen}
            aria-label="Toggle menu"
          >
            <span class="block w-6 h-0.5 bg-white transition-all duration-300" class:rotate-45={mobileMenuOpen} class:translate-y-2={mobileMenuOpen}></span>
            <span class="block w-6 h-0.5 bg-white transition-all duration-300" class:opacity-0={mobileMenuOpen}></span>
            <span class="block w-6 h-0.5 bg-white transition-all duration-300" class:-rotate-45={mobileMenuOpen} class:-translate-y-2={mobileMenuOpen}></span>
          </button>
        </div>

        <!-- Middle: Navigation Buttons - Centered -->
        <nav class="hidden md:flex items-center absolute left-1/2 transform -translate-x-1/2 gap-4 xl:gap-6 2xl:gap-8">
          {#each ["home", "about", "skills", "projects", "contact"] as section}
            <button
              class="nav-link transition-all duration-300 rounded-lg whitespace-nowrap {activeSection === section ? 'text-purple-400' : 'text-white/70 hover:text-white'} text-base xl:text-lg 2xl:text-xl px-4 py-2 xl:px-6 xl:py-3 2xl:px-8 2xl:py-4 font-medium xl:font-semibold"
              style="box-shadow: none !important; text-shadow: none !important; filter: none !important; --tw-shadow: 0 0 #0000 !important; --tw-shadow-colored: 0 0 #0000 !important; background: transparent !important;"
              class:active={activeSection === section}
              on:click={() => scrollToSection(section)}
            >
              {section === "home" ? "Home" :
               section === "about" ? "About" :
               section === "skills" ? "Skills" :
               section === "projects" ? "Projects" : "Contact"}
            </button>
          {/each}
        </nav>

        <!-- Right: Get In Touch Button - Edge of Right -->
        <div class="hidden md:block flex-shrink-0">
          <button
            class="text-white font-semibold hover:scale-105 transition-all duration-300 whitespace-nowrap rounded-xl border border-purple-400/40 hover:border-purple-400/70"
            style="box-shadow: none !important; text-shadow: none !important; filter: none !important; --tw-shadow: 0 0 #0000 !important; --tw-shadow-colored: 0 0 #0000 !important; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%) !important; padding: 0.65rem 1.25rem !important; font-size: 1rem !important;"
            on:click={() => scrollToSection("contact")}
          >
            Get In Touch
          </button>
        </div>
      </div>
    </div>

    <!-- Mobile Menu -->
    {#if mobileMenuOpen}
      <div class="md:hidden glass-strong border-t border-white/10 animate-slideDown">
        <nav class="flex flex-col gap-2 p-4">
          {#each ["home", "about", "skills", "projects", "contact"] as section}
            <button
              class="nav-link px-4 py-3 text-left text-lg font-medium transition-all duration-300 rounded-lg {activeSection === section ? 'text-purple-400 bg-white/10' : 'text-white/70 hover:text-white hover:bg-white/5'}"
              class:active={activeSection === section}
              on:click={() => { scrollToSection(section); mobileMenuOpen = false; }}
            >
              {section === "home" ? "Home" :
               section === "about" ? "About" :
               section === "skills" ? "Skills" :
               section === "projects" ? "Projects" : "Contact"}
            </button>
          {/each}
        </nav>
      </div>
    {/if}
  </header>

  <!-- Hero Section -->
  <section id="home" class="min-h-screen w-full flex items-center justify-center" style="padding-top: 5rem !important; padding-bottom: 4rem !important; margin: 0 !important; border: none !important;">
    <div class="mx-auto max-w-7xl xl:max-w-[90rem] 2xl:max-w-[100rem] px-4 sm:px-6 lg:px-8 xl:px-12 2xl:px-16 py-4 md:py-6 xl:py-12 2xl:py-16 flex flex-col md:flex-row items-center justify-center gap-12 md:gap-16 lg:gap-24 xl:gap-32 2xl:gap-40" style="margin: 0 !important;">
      
      <div class="w-64 md:w-80 lg:w-96 xl:w-[28rem] 2xl:w-[32rem] h-64 md:h-80 lg:h-96 xl:h-[28rem] 2xl:h-[32rem] rounded-3xl overflow-hidden flex-shrink-0 hero-img shadow-2xl">
        <img src="../img/photo_2023-01-12_01-11-36.jpg" alt="Ibrahem Khalil" class="w-full h-full object-cover" />
      </div>

      <div class="flex flex-col items-center text-center gap-5 md:gap-6 xl:gap-8 2xl:gap-10 max-w-2xl xl:max-w-3xl 2xl:max-w-4xl">
        <div class="inline-block mb-3 xl:mb-4 2xl:mb-5">
          <span class="text-purple-400 font-semibold tracking-widest uppercase glass rounded-full" style="font-size: 0.8rem !important; padding: 0.4rem 0.85rem !important;">Web Developer</span>
        </div>
        <h1 class="text-4xl md:text-5xl lg:text-6xl xl:text-7xl 2xl:text-8xl font-black leading-tight name-hover gradient-text mb-5 xl:mb-6 2xl:mb-8">
          Ibrahem Khalil
        </h1>
        <p class="text-base md:text-lg xl:text-xl 2xl:text-2xl text-slate-300 leading-relaxed max-w-xl xl:max-w-2xl 2xl:max-w-3xl mb-6 xl:mb-8 2xl:mb-10">
          Crafting exceptional digital experiences with modern web technologies. 
          Specialized in building responsive, high-performance applications using 
          <span class="text-purple-400 font-semibold">Svelte</span>, 
          <span class="text-purple-400 font-semibold">JavaScript</span>, and 
          <span class="text-purple-400 font-semibold">TailwindCSS</span>.
        </p>
        <div class="flex flex-col sm:flex-row gap-4 md:gap-5 xl:gap-6 2xl:gap-8 mt-4 xl:mt-6 2xl:mt-8">
          <button
            class="btn-premium rounded-xl text-white font-semibold hover:scale-105 transition-transform"
            style="box-shadow: none !important; font-size: 1.4rem !important; padding: 1.2rem 2.5rem !important;"
            on:click={() => scrollToSection("contact")}
          >
            Hire Me
          </button>
          <button
            class="glass rounded-xl text-white font-semibold hover:bg-white/10 transition-all hover:scale-105 border border-white/20 hover:border-white/40"
            style="box-shadow: none !important; font-size: 1.4rem !important; padding: 1.2rem 2.5rem !important;"
            on:click={() => scrollToSection("projects")}
          >
            View Work
          </button>
        </div>
      </div>
    </div>
  </section>

  <!-- About Section -->
  <section id="about" class="w-full flex items-center justify-center px-4 sm:px-6 lg:px-8 xl:px-12 2xl:px-16" style="padding-top: 4rem !important; padding-bottom: 4rem !important; min-height: auto !important; margin: 0 !important; border: none !important;">
    <div class="max-w-6xl xl:max-w-7xl 2xl:max-w-[90rem] mx-auto w-full text-center">
      <h2 class="text-3xl md:text-4xl lg:text-5xl xl:text-6xl 2xl:text-7xl font-bold mb-6 md:mb-8 xl:mb-10 2xl:mb-12 gradient-text">About Me</h2>
      <div class="flex flex-col items-center gap-6 md:gap-8 xl:gap-10 2xl:gap-12">
        <p class="text-lg md:text-xl lg:text-2xl xl:text-3xl 2xl:text-4xl text-slate-300 leading-relaxed max-w-4xl xl:max-w-5xl 2xl:max-w-6xl" style="margin-top: 0.5rem !important;">
          I'm <span class="text-purple-400 font-semibold">Ibrahem</span>, a passionate web developer with extensive experience in building modern, responsive, and high-performance web applications. I specialize in creating clean, efficient, and user-friendly digital experiences using the latest web technologies, including HTML, CSS, JavaScript, Svelte, and TailwindCSS.
        </p>
        <p class="text-lg md:text-xl lg:text-2xl xl:text-3xl 2xl:text-4xl text-slate-300 leading-relaxed max-w-4xl xl:max-w-5xl 2xl:max-w-6xl">
          I thrive on turning ideas into functional projects, continuously learning new tools, and bringing innovative solutions to complex problems. My goal is to create web experiences that not only look stunning but also perform flawlessly across all devices.
        </p>
      </div>
    </div>
  </section>

  <!-- Skills Section -->
  <section id="skills" class="w-full flex items-center justify-center px-4 sm:px-6 lg:px-8 xl:px-12 2xl:px-16 overflow-y-auto" style="padding-top: 4rem !important; padding-bottom: 4rem !important; min-height: auto !important; margin: 0 !important; border: none !important;">
    <div class="max-w-7xl xl:max-w-[90rem] 2xl:max-w-[100rem] mx-auto w-full">
      <div class="text-center">
        <h2 class="text-3xl md:text-4xl lg:text-5xl xl:text-6xl 2xl:text-7xl font-bold gradient-text" style="margin-bottom: 1rem !important; line-height: 1.2 !important; padding-bottom: 0.25rem !important;">Skills & Expertise</h2>
        <p class="text-slate-400 text-base md:text-lg xl:text-xl 2xl:text-2xl" style="margin-top: 0.5rem !important; margin-bottom: 1.5rem !important;">Technologies I work with</p>
      </div>
      <div class="grid sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-3 2xl:grid-cols-3 gap-5 md:gap-6 xl:gap-8 2xl:gap-10 justify-items-center" style="margin-top: 2rem !important;">
      {#each skills as skill}
        <div class="glass-strong rounded-2xl hover:scale-105 transition-transform duration-300 w-full max-w-sm shadow-lg" style="padding: 1rem 1.25rem !important;">
          <div class="flex items-center justify-between" style="margin-bottom: 0.75rem !important;">
            <div class="flex items-center gap-2.5">
              <i class="{skill.icon} text-purple-400" style="font-size: 1.75rem !important;"></i>
              <h3 class="font-semibold" style="font-size: 1rem !important;">{skill.name}</h3>
            </div>
            <span class="text-purple-400 font-bold" style="font-size: 1rem !important;">{skill.level}%</span>
          </div>
          <div class="w-full bg-slate-800/50 rounded-full overflow-hidden shadow-inner" style="height: 0.5rem !important;">
            <div 
              class="skill-bar h-full bg-gradient-to-r from-purple-500 to-pink-500 rounded-full shadow-lg"
              data-width={skill.level}
              style="width: 0%"
            ></div>
          </div>
        </div>
      {/each}
    </div>
    </div>
  </section>

  <!-- Projects Section -->
  <section id="projects" class="w-full flex items-center justify-center px-4 sm:px-6 lg:px-8 xl:px-12 2xl:px-16 overflow-y-auto" style="padding-top: 4rem !important; padding-bottom: 4rem !important; min-height: auto !important; margin: 0 !important; border: none !important;">
    <div class="max-w-7xl xl:max-w-[90rem] 2xl:max-w-[100rem] mx-auto w-full">
      <div class="text-center">
        <h2 class="text-3xl md:text-4xl lg:text-5xl xl:text-6xl 2xl:text-7xl font-bold gradient-text" style="margin-bottom: 1rem !important; line-height: 1.2 !important; padding-bottom: 0.25rem !important;">Featured Projects</h2>
        <p class="text-center text-slate-400 text-base md:text-lg xl:text-xl 2xl:text-2xl" style="margin-top: 0.5rem !important; margin-bottom: 1.5rem !important;">Showcasing my latest work and creative solutions</p>
      </div>
      <div class="grid sm:grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-3 2xl:grid-cols-3 gap-5 md:gap-6 xl:gap-8 2xl:gap-10 justify-items-center" style="margin-top: 2rem !important;">
      <a href="https://github.com/ibrahem98k/Skywalker-Cinema-App" target="_blank" class="project-card glass-strong rounded-2xl overflow-hidden shadow-2xl hover:shadow-purple-500/20 hover:scale-105 transition-all duration-500 group w-full max-w-sm md:max-w-md lg:max-w-lg">
        <div class="relative overflow-hidden">
          <img src="../img/jeremy-yap-J39X2xX_8CQ-unsplash.jpg" class="w-full h-56 md:h-64 lg:h-72 object-cover group-hover:scale-110 transition-transform duration-500" />
          <div class="absolute inset-0 bg-gradient-to-t from-black/80 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>
        </div>
        <div style="padding: 1.25rem 1.5rem !important;">
          <h3 class="font-bold group-hover:text-purple-400 transition-colors" style="font-size: 1.25rem !important; margin-bottom: 0.75rem !important;">Skywalker Cinema App</h3>
          <p class="text-slate-300 leading-relaxed" style="font-size: 0.95rem !important; margin-bottom: 1rem !important; line-height: 1.5 !important;">A modern cinema booking application with seamless user experience and real-time seat selection.</p>
          <div class="flex flex-wrap gap-1.5">
            <span class="bg-purple-500/20 text-purple-300 rounded-full font-medium" style="font-size: 0.75rem !important; padding: 0.35rem 0.7rem !important;">C#</span>
          </div>
        </div>
      </a>

      <a href="https://github.com/ibrahem98k/CART" target="_blank" class="project-card glass-strong rounded-2xl overflow-hidden shadow-2xl hover:shadow-purple-500/20 hover:scale-105 transition-all duration-500 group w-full max-w-sm md:max-w-md lg:max-w-lg">
        <div class="relative overflow-hidden">
          <img src="../img/oleksii-s-23gXCebItDQ-unsplash.jpg" class="w-full h-56 md:h-64 lg:h-72 object-cover group-hover:scale-110 transition-transform duration-500" />
          <div class="absolute inset-0 bg-gradient-to-t from-black/80 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>
        </div>
        <div style="padding: 1.25rem 1.5rem !important;">
          <h3 class="font-bold group-hover:text-purple-400 transition-colors" style="font-size: 1.25rem !important; margin-bottom: 0.75rem !important;">CART</h3>
          <p class="text-slate-300 leading-relaxed" style="font-size: 0.95rem !important; margin-bottom: 1rem !important; line-height: 1.5 !important;">An elegant shopping cart application with intuitive interface and smooth checkout process.</p>
          <div class="flex flex-wrap gap-1.5">
            <span class="bg-purple-500/20 text-purple-300 rounded-full font-medium" style="font-size: 0.75rem !important; padding: 0.35rem 0.7rem !important;">HTML</span>
            <span class="bg-purple-500/20 text-purple-300 rounded-full font-medium" style="font-size: 0.75rem !important; padding: 0.35rem 0.7rem !important;">CSS</span>
          </div>
        </div>
      </a>

      <a href="https://github.com/ibrahem98k/calculator" target="_blank" class="project-card glass-strong rounded-2xl overflow-hidden shadow-2xl hover:shadow-purple-500/20 hover:scale-105 transition-all duration-500 group w-full max-w-sm md:max-w-md lg:max-w-lg">
        <div class="relative overflow-hidden">
          <img src="../img/gvz-42-z47okydJjGs-unsplash.jpg" class="w-full h-56 md:h-64 lg:h-72 object-cover group-hover:scale-110 transition-transform duration-500" />
          <div class="absolute inset-0 bg-gradient-to-t from-black/80 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>
        </div>
        <div style="padding: 1.25rem 1.5rem !important;">
          <h3 class="font-bold group-hover:text-purple-400 transition-colors" style="font-size: 1.25rem !important; margin-bottom: 0.75rem !important;">Calculator</h3>
          <p class="text-slate-300 leading-relaxed" style="font-size: 0.95rem !important; margin-bottom: 1rem !important; line-height: 1.5 !important;">A sleek and functional calculator with modern design and smooth animations.</p>
          <div class="flex flex-wrap gap-1.5">
            <span class="bg-purple-500/20 text-purple-300 rounded-full font-medium" style="font-size: 0.75rem !important; padding: 0.35rem 0.7rem !important;">Svelte</span>
            <span class="bg-purple-500/20 text-purple-300 rounded-full font-medium" style="font-size: 0.75rem !important; padding: 0.35rem 0.7rem !important;">JavaScript</span>
            <span class="bg-purple-500/20 text-purple-300 rounded-full font-medium" style="font-size: 0.75rem !important; padding: 0.35rem 0.7rem !important;">CSS</span>
          </div>
        </div>
      </a>
    </div>
    </div>
  </section>

  <!-- Contact Section -->
  <section id="contact" class="w-full flex items-center justify-center px-4 sm:px-6 lg:px-8 xl:px-12 2xl:px-16" style="padding-top: 4rem !important; padding-bottom: 4rem !important; min-height: auto !important; margin: 0 !important; border: none !important;">
    <div class="max-w-3xl mx-auto w-full">
      <h2 class="text-3xl md:text-4xl lg:text-5xl xl:text-6xl 2xl:text-7xl font-bold text-center gradient-text" style="margin-bottom: 1rem !important; word-wrap: break-word; overflow-wrap: break-word; white-space: normal; line-height: 1.2 !important; padding-bottom: 0.25rem !important;">Let's Work Together</h2>
      <p class="text-center text-slate-400 text-base md:text-lg" style="margin-top: 1rem !important; margin-bottom: 2rem !important;">Have a project in mind? I'd love to hear from you.</p>
      
      <div class="glass-strong rounded-3xl" style="padding: 1.5rem 2rem !important;">
        {#if formStatus.success}
          <div class="bg-green-500/20 border border-green-500/50 rounded-xl p-4 mb-4 text-center">
            <p class="text-green-400 text-sm">{formStatus.message}</p>
          </div>
        {/if}
        {#if formStatus.error}
          <div class="bg-red-500/20 border border-red-500/50 rounded-xl p-4 mb-4 text-center">
            <p class="text-red-400 text-sm">{formStatus.message}</p>
          </div>
        {/if}
        <form on:submit={handleFormSubmit} class="flex flex-col" style="gap: 1.25rem !important;">
          <div class="grid md:grid-cols-2" style="gap: 1rem !important;">
            <div>
              <label class="block font-semibold text-purple-400" style="font-size: 0.9rem !important; margin-bottom: 0.5rem !important;">Your Name</label>
              <input 
                type="text" 
                placeholder="John Doe" 
                class="input-premium w-full rounded-xl text-white placeholder-slate-500"
                style="font-size: 0.85rem !important; padding: 0.6rem 0.9rem !important;"
                bind:value={formData.name}
                required
                disabled={formStatus.submitting}
              />
            </div>
            <div>
              <label class="block font-semibold text-purple-400" style="font-size: 0.9rem !important; margin-bottom: 0.5rem !important;">Your Email</label>
              <input 
                type="email" 
                placeholder="john@example.com" 
                class="input-premium w-full rounded-xl text-white placeholder-slate-500"
                style="font-size: 0.85rem !important; padding: 0.6rem 0.9rem !important;"
                bind:value={formData.email}
                required
                disabled={formStatus.submitting}
              />
            </div>
          </div>
          <div>
            <label class="block font-semibold text-purple-400" style="font-size: 0.9rem !important; margin-bottom: 0.5rem !important;">Message</label>
            <textarea 
              rows="6" 
              placeholder="Tell me about your project..." 
              class="input-premium w-full rounded-xl text-white placeholder-slate-500 resize-none"
              style="font-size: 0.85rem !important; padding: 0.6rem 0.9rem !important;"
              bind:value={formData.message}
              required
              disabled={formStatus.submitting}
            ></textarea>
          </div>
          <button 
            type="submit" 
            class="btn-premium w-full md:w-auto mx-auto text-white font-semibold rounded-xl hover:scale-105 transition-transform disabled:opacity-50 disabled:cursor-not-allowed"
            style="font-size: 0.9rem !important; padding: 0.6rem 1.5rem !important; margin-top: 0.5rem !important; box-shadow: none !important;"
            disabled={formStatus.submitting}
          >
            {formStatus.submitting ? "Sending..." : "Send Message"}
          </button>
        </form>
      </div>
    </div>
  </section>

  <!-- Floating Logos -->
  <div class="pointer-events-none fixed inset-0 overflow-hidden">
    <img src="./img/4459321c4e61adc58c584aa00788d148-removebg-preview.png" class="floating-logo" style="top:30%; left:2%;" />
    <img src="./img/a74235683c71b9a1d55788c94ac8bdfa-removebg-preview.png" class="floating-logo" style="top:18%; left:78%;" />
    <img src="./img/a8c934a6bd243de6e63aaf072c69013c-removebg-preview.png" class="floating-logo" style="top:73%; left:80%;" />
    <img src="./img/c-sharp-800x800.png" class="floating-logo" style="top:78%; left:40%;" />
    <img src="./img/logo-1675239_19233330.png" class="floating-logo" style="top:32%; left:35%;" />
  </div>

  <!-- Premium Footer -->
  <footer class="glass-strong border-t border-white/10" style="margin: 0 !important; padding-top: 0 !important; padding-bottom: 0 !important; border-top: 1px solid rgba(255, 255, 255, 0.1) !important;">
    <div class="w-full px-4 sm:px-6 lg:px-8 xl:px-12 2xl:px-16 flex flex-col md:flex-row justify-between items-center gap-6" style="padding-top: 1.5rem !important; padding-bottom: 1.5rem !important;">
      <p class="text-slate-400">&copy; {new Date().getFullYear()} Ibrahem Khalil. All rights reserved.</p>
      <div class="flex gap-6 ml-auto">
        <a href="https://github.com/ibrahem98k" target="_blank" rel="noopener noreferrer" class="w-12 h-12 glass rounded-full flex items-center justify-center hover:bg-purple-500/20 hover:scale-110 transition-all duration-300 group">
          <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-slate-400 group-hover:text-purple-400 transition-colors" fill="currentColor" viewBox="0 0 24 24">
            <path d="M12 0C5.372 0 0 5.373 0 12c0 5.303 3.438 9.8 8.205 11.387.6.113.82-.26.82-.577 0-.285-.01-1.04-.016-2.04-3.338.725-4.042-1.61-4.042-1.61-.546-1.387-1.333-1.756-1.333-1.756-1.09-.745.083-.73.083-.73 1.205.084 1.84 1.237 1.84 1.237 1.07 1.834 2.807 1.304 3.492.997.108-.775.418-1.305.762-1.604-2.665-.305-5.466-1.334-5.466-5.93 0-1.31.47-2.38 1.235-3.22-.123-.303-.535-1.523.117-3.176 0 0 1.008-.322 3.3 1.23a11.47 11.47 0 013.003-.404c1.02.005 2.045.138 3.003.404 2.29-1.552 3.296-1.23 3.296-1.23.653 1.653.241 2.873.118 3.176.77.84 1.233 1.91 1.233 3.22 0 4.61-2.805 5.624-5.475 5.922.43.372.814 1.104.814 2.222 0 1.604-.015 2.896-.015 3.286 0 .319.216.694.825.576C20.565 21.796 24 17.3 24 12c0-6.627-5.373-12-12-12z"/>
          </svg>
        </a>
        <a href="https://www.linkedin.com/in/ibrahem-khalil-6361b7386/" target="_blank" rel="noopener noreferrer" class="w-12 h-12 glass rounded-full flex items-center justify-center hover:bg-purple-500/20 hover:scale-110 transition-all duration-300 group">
          <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-slate-400 group-hover:text-purple-400 transition-colors" fill="currentColor" viewBox="0 0 24 24">
            <path d="M20.447 20.452h-3.554v-5.569c0-1.328-.025-3.037-1.85-3.037-1.851 0-2.134 1.445-2.134 2.938v5.668H9.356V9h3.414v1.561h.049c.476-.9 1.637-1.85 3.367-1.85 3.6 0 4.268 2.369 4.268 5.455v6.286zM5.337 7.433a2.062 2.062 0 01-2.063-2.063c0-1.137.926-2.062 2.063-2.062 1.138 0 2.063.925 2.063 2.062a2.062 2.062 0 01-2.063 2.063zm1.777 13.019H3.56V9h3.554v11.452zM22.225 0H1.771C.792 0 0 .77 0 1.722v20.555C0 23.23.792 24 1.771 24h20.451C23.2 24 24 23.23 24 22.278V1.722C24 .77 23.2 0 22.225 0z"/>
          </svg>
        </a>
      </div>
    </div>
  </footer>

</main>
