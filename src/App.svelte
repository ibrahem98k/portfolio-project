<script>
  import { onMount } from "svelte";
  import gsap from "gsap";

  let activeSection = "home";

  function scrollToSection(section) {
    activeSection = section;
    const el = document.getElementById(section);
    if (el) el.scrollIntoView({ behavior: "smooth" });
  }

  onMount(() => {
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

    const sections = ["home", "about", "projects", "contact"];
    const handleScroll = () => {
      const scrollPos = window.scrollY + 150;
      for (const section of sections) {
        const el = document.getElementById(section);
        if (el && scrollPos >= el.offsetTop) {
          activeSection = section;
        }
      }
    };
    window.addEventListener("scroll", handleScroll);
    return () => window.removeEventListener("scroll", handleScroll);
  });
</script>

<style>
  @keyframes logoGlow {
    0%, 100% { filter: drop-shadow(0 0 2px #0ff) drop-shadow(0 0 4px #0ff); }
    50% { filter: drop-shadow(0 0 6px #0ff) drop-shadow(0 0 10px #00f); }
  }
  .glow-logo {
    animation: logoGlow 3s infinite ease-in-out;
  }

  
  .name-hover {
    transition: all 0.5s ease-in-out;
    display: inline-block;
    transform-origin: center;
    cursor: pointer;
  }
  .name-hover:hover {
    transform: scale(1.1);
    color: #7f5af0;
    text-shadow: 0 0 8px #5f006b, 0 0 15px #120030;
  }

  
  .hero-img {
    transition: all 0.5s ease-in-out;
    cursor: pointer;
  }
  .hero-img:hover {
    transform: scale(1.05);
    box-shadow: 0 0 20px rgba(127, 90, 240, 0.5);
  }

  .hero-text-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    gap: 1.5rem;
    max-width: 600px;
  }

  
  .floating-logo {
    position: absolute;
    width: 80px;
    height: 80px;
    pointer-events: none;
  }
</style>

<main class="min-h-screen bg-gradient-to-b from-[#0C0E2B] via-[#170F2F] to-[#0C0E2B] text-white">

  
  <header class="fixed inset-x-0 top-0 bg-[#0C0E2B] z-50">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <div class="flex items-center justify-between h-24">
        <div class="flex items-center gap-4">
          <div class="w-25 h-25 rounded overflow-hidden glow-logo">
            <img src="../img/lc-or-cl-bold-monogram-logo-vector-48182063-removebg-preview.png" alt="Logo" class="w-full h-full object-cover" />
          </div>
        </div>

        <nav class="hidden md:flex gap-8">
          {#each ["home", "about", "projects", "contact"] as section}
            <button
              class="px-3 py-2 rounded text-xl md:text-2xl transition-all duration-300 transform hover:scale-105 hover:text-blue-400"
              class:text-blue-500={activeSection === section}
              class:text-white={activeSection !== section}
              on:click={() => scrollToSection(section)}
            >
              {section === "home" ? "Home" :
               section === "about" ? "About Me" :
               section === "projects" ? "Projects" : "Contact"}
            </button>
          {/each}
        </nav>

        <div>
          <button
            class="text-lg md:text-xl rounded-full px-6 py-3 md:px-8 md:py-4 bg-blue-600 text-white hover:bg-blue-500 hover:scale-105 transition transform duration-300 ease-in-out"
            on:click={() => scrollToSection("contact")}
          >
            Contact
          </button>
        </div>
      </div>
    </div>
  </header>

  
  <section id="home" class="pt-20 md:pt-32 w-full">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8 py-12 flex flex-col md:flex-row items-center gap-12 md:gap-20">
      
      
      <div class="w-64 md:w-72 h-64 md:h-72 rounded overflow-hidden flex-shrink-0">
        <img src="../img/photo_2023-01-12_01-11-36.jpg" alt="Ibrahem Khalil" class="w-full h-full object-cover hero-img" />
      </div>

      <div class="hero-text-wrapper">
        <h1 class="text-3xl md:text-5xl font-bold leading-tight name-hover">Ibrahem Khalil</h1>
        <p class="text-md md:text-lg text-slate-300 max-w-xl">
          I'm a passionate web developer with extensive experience in building modern, responsive, and high-performance web applications. I specialize in creating clean, efficient, and user-friendly digital experiences using the latest web technologies including HTML, CSS, JavaScript, Svelte, and TailwindCSS.
        </p>
        <button
          class="text-lg md:text-xl rounded-full px-6 py-3 md:px-8 md:py-4 bg-blue-600 text-white hover:bg-blue-500 hover:scale-105 transition transform duration-300 ease-in-out"
          on:click={() => scrollToSection("contact")}
        >
          Hire Me
        </button>
      </div>
    </div>
  </section>

  
  <section id="about" class="pt-12 md:pt-16 px-4 sm:px-6 lg:px-8 max-w-7xl mx-auto">
    <h2 class="text-3xl md:text-4xl font-bold mb-4 md:mb-6">About Me</h2>
    <p class="text-md md:text-lg text-slate-300 leading-relaxed max-w-3xl">
      I'm Ibrahem, a passionate web developer with extensive experience in building modern, responsive, and high-performance web applications. I specialize in creating clean, efficient, and user-friendly digital experiences using the latest web technologies, including HTML, CSS, JavaScript, Svelte, and TailwindCSS. I thrive on turning ideas into functional projects, continuously learning new tools, and bringing innovative solutions to complex problems.
    </p>
  </section>

  
  <section id="projects" class="pt-16 md:pt-24 px-4 sm:px-6 lg:px-8 max-w-7xl mx-auto">
    <h2 class="text-3xl md:text-4xl font-bold mb-8">Projects</h2>
    <div class="grid md:grid-cols-2 lg:grid-cols-3 gap-8">
      <a href="https://github.com/ibrahem98k/Skywalker-Cinema-App" target="_blank" class="block bg-slate-800 rounded-lg overflow-hidden shadow hover:shadow-lg hover:scale-105 transition transform duration-300">
        <img src="../img/jeremy-yap-J39X2xX_8CQ-unsplash.jpg" class="w-full h-48 md:h-56 object-cover" />
        <div class="p-4">
          <h3 class="text-lg md:text-xl font-semibold mb-2">Skywalker Cinema App</h3>
          <p class="text-sm md:text-md text-slate-300">A cinema booking app.</p>
        </div>
      </a>
      <a href="https://github.com/ibrahem98k/CART" target="_blank" class="block bg-slate-800 rounded-lg overflow-hidden shadow hover:shadow-lg hover:scale-105 transition transform duration-300">
        <img src="../img/oleksii-s-23gXCebItDQ-unsplash.jpg" class="w-full h-48 md:h-56 object-cover" />
        <div class="p-4">
          <h3 class="text-lg md:text-xl font-semibold mb-2">CART</h3>
          <p class="text-sm md:text-md text-slate-300">A shopping cart app.</p>
        </div>
      </a>
      <a href="https://github.com/ibrahem98k/calculator" target="_blank" class="block bg-slate-800 rounded-lg overflow-hidden shadow hover:shadow-lg hover:scale-105 transition transform duration-300">
        <img src="../img/gvz-42-z47okydJjGs-unsplash.jpg" class="w-full h-48 md:h-56 object-cover" />
        <div class="p-4">
          <h3 class="text-lg md:text-xl font-semibold mb-2">Calculator</h3>
          <p class="text-sm md:text-md text-slate-300">A simple calculator.</p>
        </div>
      </a>
    </div>
  </section>

  
  <section id="contact" class="pt-16 md:pt-24 pb-16 px-4 sm:px-6 lg:px-8">
    <div class="max-w-3xl mx-auto text-center">
      <h2 class="text-3xl md:text-4xl font-bold mb-6">Contact Me</h2>
      <form action="mailto:ibrahemkhalil123123@gmail.com" method="POST" encType="text/plain" class="flex flex-col gap-4 mx-auto">
        <input type="text" name="name" placeholder="Your Name" class="px-4 py-2 rounded border border-gray-300 focus:ring-2 focus:ring-blue-500 focus:outline-none" />
        <input type="email" name="email" placeholder="Your Email" class="px-4 py-2 rounded border border-gray-300 focus:ring-2 focus:ring-blue-500 focus:outline-none" />
        <textarea name="message" rows="5" placeholder="Your Message" class="px-4 py-2 rounded border border-gray-300 focus:ring-2 focus:ring-blue-500 focus:outline-none"></textarea>
        <button type="submit" class="px-6 py-3 md:px-8 md:py-4 bg-blue-600 text-white rounded-full hover:bg-blue-500 hover:scale-105 transition transform duration-300 ease-in-out">
          Send Message
        </button>
      </form>
    </div>
  </section>

  
  <div class="pointer-events-none">
    <img src="./img/4459321c4e61adc58c584aa00788d148-removebg-preview.png" class="floating-logo" style="top:30%; left:2%;" />
    <img src="./img/a74235683c71b9a1d55788c94ac8bdfa-removebg-preview.png" class="floating-logo" style="top:18%; left:78%;" />
    <img src="./img/a8c934a6bd243de6e63aaf072c69013c-removebg-preview.png" class="floating-logo" style="top:73%; left:80%;" />
    <img src="./img/c-sharp-800x800.png" class="floating-logo" style="top:78%; left:40%;" />
    <img src="./img/logo-1675239_19233330.png" class="floating-logo" style="top:32%; left:35%;" />
  </div>

  
  <footer class="bg-[#0C0E2B] text-white py-8 mt-16">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex flex-col md:flex-row justify-between items-center gap-6">
      <p>&copy; {new Date().getFullYear()} Ibrahem Khalil. All rights reserved.</p>
      <div class="flex gap-6 text-3xl">
        
        <a href="https://github.com/ibrahem98k" target="_blank" rel="noopener noreferrer" class="hover:text-blue-400 transition-colors">
          <svg xmlns="http://www.w3.org/2000/svg" class="w-8 h-8" fill="currentColor" viewBox="0 0 24 24">
            <path d="M12 0C5.372 0 0 5.373 0 12c0 5.303 3.438 9.8 8.205 11.387.6.113.82-.26.82-.577 0-.285-.01-1.04-.016-2.04-3.338.725-4.042-1.61-4.042-1.61-.546-1.387-1.333-1.756-1.333-1.756-1.09-.745.083-.73.083-.73 1.205.084 1.84 1.237 1.84 1.237 1.07 1.834 2.807 1.304 3.492.997.108-.775.418-1.305.762-1.604-2.665-.305-5.466-1.334-5.466-5.93 0-1.31.47-2.38 1.235-3.22-.123-.303-.535-1.523.117-3.176 0 0 1.008-.322 3.3 1.23a11.47 11.47 0 013.003-.404c1.02.005 2.045.138 3.003.404 2.29-1.552 3.296-1.23 3.296-1.23.653 1.653.241 2.873.118 3.176.77.84 1.233 1.91 1.233 3.22 0 4.61-2.805 5.624-5.475 5.922.43.372.814 1.104.814 2.222 0 1.604-.015 2.896-.015 3.286 0 .319.216.694.825.576C20.565 21.796 24 17.3 24 12c0-6.627-5.373-12-12-12z"/>
          </svg>
        </a>

      
        <a href="https://linkedin.com/in/ibrahem98k" target="_blank" rel="noopener noreferrer" class="hover:text-blue-400 transition-colors">
          <svg xmlns="http://www.w3.org/2000/svg" class="w-8 h-8" fill="currentColor" viewBox="0 0 24 24">
            <path d="M20.447 20.452h-3.554v-5.569c0-1.328-.025-3.037-1.85-3.037-1.851 0-2.134 1.445-2.134 2.938v5.668H9.356V9h3.414v1.561h.049c.476-.9 1.637-1.85 3.367-1.85 3.6 0 4.268 2.369 4.268 5.455v6.286zM5.337 7.433a2.062 2.062 0 01-2.063-2.063c0-1.137.926-2.062 2.063-2.062 1.138 0 2.063.925 2.063 2.062a2.062 2.062 0 01-2.063 2.063zm1.777 13.019H3.56V9h3.554v11.452zM22.225 0H1.771C.792 0 0 .77 0 1.722v20.555C0 23.23.792 24 1.771 24h20.451C23.2 24 24 23.23 24 22.278V1.722C24 .77 23.2 0 22.225 0z"/>
          </svg>
        </a>
      </div>
    </div>
  </footer>

</main>
