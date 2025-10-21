<template>
  <div class="relative min-h-screen overflow-hidden bg-gradient-to-r from-cyan-700 via-blue-500 to-cyan-400 animate-gradient">
    
    <!-- Floating particles -->
    <div
      v-for="(particle, i) in particles"
      :key="i"
      class="absolute rounded-full opacity-30 bg-white z-10"
      :style="particle.style"
    ></div>

    <!-- Main content -->
    <div class="relative z-20 flex flex-col items-center">
      
      <!-- Header -->
<header class="w-full max-w-6xl px-4 sm:px-6 md:px-8 pt-6 flex flex-col sm:flex-row items-start justify-between relative">
  <!-- Name + Slogan -->
  <div class="flex flex-col items-start gap-2">
    <h1 class="text-4xl sm:text-5xl md:text-6xl font-bold text-white text-left">
      {{ texts.name }}
    </h1>
    <p class="text-sm sm:text-base md:text-lg italic text-white text-left">
      {{ texts.slogan }}
    </p>
  </div>

  <!-- Language toggle -->
  <button
    @click="toggleLanguage"
    class="absolute top-4 right-4 sm:static sm:ml-auto bg-white text-cyan-800 font-semibold px-4 py-2 rounded-full shadow-lg hover:scale-110 hover:bg-cyan-100 transition-transform duration-300"
  >
    🌐 {{ currentLang === "ka" ? "EN" : "KA" }}
  </button>
</header>



      <!-- Shells -->
      <div class="absolute mt-10 shells-container flex flex-wrap justify-center gap-4 sm:gap-6">
        <div
          v-for="(shell, i) in shells"
          :key="i"
          class="skill-box text-lg sm:text-xl md:text-2xl p-2 sm:p-3 md:p-4"
          :style="{ transform: `translateY(${waveY[i]}px)` }"
        >
          {{ shell }}
        </div>
      </div>

      <!-- Products Section -->
      <section class="w-full max-w-6xl px-4 sm:px-6 md:px-8 mt-4">
        <h2 class="text-2xl sm:text-3xl md:text-4xl font-semibold text-center mb-8 text-white">
          {{ texts.productsTitle }}
        </h2>
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-6">
          <div
            v-for="(prod, i) in products"
            :key="i"
            class="relative w-full aspect-square overflow-hidden rounded-xl shadow-lg"
          >
            <img :src="prod.img" :alt="prod.title" class="absolute inset-0 w-full h-full object-cover" />
            <div class="absolute inset-0 flex flex-col justify-end items-end bg-black/30 px-4 pb-4">
              <button
                class="bg-white text-cyan-800 font-semibold px-4 py-1.5 rounded-xl hover:bg-cyan-100 hover:scale-110 transition-transform duration-300"
              >
                {{ texts.buy }}
              </button>
            </div>
          </div>
        </div>
      </section>

      <!-- Wave SVG at bottom -->
      <svg
        class="w-full mt-16"
        viewBox="0 0 1440 150"
        preserveAspectRatio="none"
      >
        <path
          ref="wavePath"
          fill="#0ea5e9"
          d="M0,30 C360,90 1080,-30 1440,30 L1440,150 L0,150 Z"
        ></path>
      </svg>

    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

/* Language toggle */
const currentLang = ref("en");
const langData = {
  ka: { name: "ზვირთი", slogan: "შეივსე ბუნების ძალით", productsTitle: "პროდუქტები", prod1:"პროდუქტი 1", prod2:"პროდუქტი 2", prod3:"პროდუქტი 3", prod4:"პროდუქტი 4", prod5:"პროდუქტი 5", prod6:"პროდუქტი 6", prod7:"პროდუქტი 7", prod8:"პროდუქტი 8", buy:"შეძენა", footer:"© 2025 ეკატო მაყაშვილი. ყველა უფლება დაცულია." },
  en: { name: "Zvirti", slogan:"Let nature fill you", productsTitle:"Products", prod1:"Product 1", prod2:"Product 2", prod3:"Product 3", prod4:"Product 4", prod5:"Product 5", prod6:"Product 6", prod7:"Product 7", prod8:"Product 8", buy:"Buy", footer:"© 2025 Zvirti. All rights reserved." },
};

const texts = computed(() => langData[currentLang.value]);
const toggleLanguage = () => currentLang.value = currentLang.value === "ka" ? "en" : "ka";

/* Shells wave animation */
const shells = ref(["🫧","🐬","🪼","🪸","🦀","𓇼"]);
const waveY = ref(shells.value.map(() => 0));
const wavePath = ref(null);
let t = 0;
const amplitude = 15;
const speed = 0.01;

function animateWave() {
  t += speed;
  if (wavePath.value) {
    const newD = `
      M0,${30 + Math.sin(t) * amplitude} 
      C360,${90 + Math.sin(t + 1) * amplitude} 
       1080,${-30 + Math.sin(t + 2) * amplitude} 
       1440,${30 + Math.sin(t + 3) * amplitude} 
      L1440,150 L0,150 Z
    `;
    wavePath.value.setAttribute("d", newD);
  }
  shells.value.forEach((_, i) => {
    waveY.value[i] = Math.sin(t + (i / shells.value.length) * Math.PI * 2) * amplitude;
  });
  requestAnimationFrame(animateWave);
}

onMounted(() => animateWave());

/* Products */
const products = computed(() => [
  { img: "prod1.JPG", title: texts.value.prod1 },
  { img: "prod2.JPG", title: texts.value.prod2 },
  { img: "prod3.jpg", title: texts.value.prod3 },
  { img: "prod4.JPG", title: texts.value.prod4 },
  { img: "prod5.jpg", title: texts.value.prod5 },
  { img: "prod6.jpg", title: texts.value.prod6 },
  { img: "prod7.JPG", title: texts.value.prod7 },
  { img: "prod8.jpg", title: texts.value.prod8 },
]);

/* Floating particles */
const particles = ref([]);
onMounted(() => {
  for (let i = 0; i < 50; i++) {
    const size = Math.random() * 20 + 5;
    const left = Math.random() * 100;
    const duration = Math.random() * 20 + 10;
    const delay = Math.random() * 10;
    particles.value.push({
      style: {
        width: `${size}px`,
        height: `${size}px`,
        left: `${left}%`,
        bottom: "-30px",
        animation: `rise ${duration}s linear infinite`,
        animationDelay: `${delay}s`,
      },
    });
  }
});
</script>

<style>
/* Gradient background animation */
@keyframes gradientShift {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}
.animate-gradient { background-size: 600% 600%; animation: gradientShift 20s ease infinite; }

/* Floating particles */
@keyframes rise {
  0% { transform: translateY(0); opacity: 0.2; }
  50% { opacity: 0.3; }
  100% { transform: translateY(-120vh); opacity: 0; }
}

.shells-container { z-index: 10; }
.skill-box {
  background: rgba(255,255,255,0.15);
  color: #e2dfd2;
  font-weight: 600;
  border-radius: 50px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 6px 18px rgba(14,165,233,0.1);
  transition: transform 0.2s;
}
.skill-box:hover {
  transform: scale(1.1);
  box-shadow: 0 12px 30px rgba(14,165,233,0.22);
  background: black;
  opacity: 0.1;
}

img { object-fit: cover; width: 100%; height: 100%; }
</style>
