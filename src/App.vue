<template>
  <div class="relative min-h-screen overflow-hidden" @mousemove="onMouseMove">
    <!-- Gradient background -->
    <div
      class="absolute inset-0 animate-gradient bg-gradient-to-r from-cyan-700 via-blue-500 to-cyan-400 z-0"
    ></div>

    <!-- Floating particles -->
    <div
      v-for="(particle, i) in particles"
      :key="i"
      class="absolute rounded-full opacity-30 bg-white z-10"
      :style="particle.style"
    ></div>

    <!-- Main content -->
    <div class="relative z-20">
      <!-- Header Section -->
      <header class="relative mt-4 mx-4">
        <button
          @click="toggleLanguage"
          class="absolute right-6 bg-white text-cyan-800 font-semibold px-4 py-2 rounded-full shadow-lg hover:scale-110 hover:bg-cyan-100 transition-transform duration-300"
        >
          🌐 {{ currentLang === "ka" ? "EN" : "KA" }}
        </button>

        <h1 class="text-5xl font-bold">{{ texts.name }}</h1>
        <p class="text-sm italic mt-2">{{ texts.slogan }}</p>
      </header>

      <!-- Shells -->
      <div
        class="shells-container flex flex-wrap justify-center gap-6 relative z-10"
      >
        <div
          v-for="(skill, i) in shells"
          :key="i"
          class="skill-box"
          :style="{ transform: `translateY(${waveY[i] - 40}px)` }"
        >
          {{ skill }}
        </div>
      </div>

      <!-- Wave Separator -->
      <svg
        class="wave-separator w-full"
        viewBox="0 0 1440 150"
        preserveAspectRatio="none"
      >
        <path
          ref="wavePath"
          fill="#0ea5e9"
          d="M0,30 C360,90 1080,-30 1440,30 L1440,150 L0,150 Z"
        ></path>
      </svg>

      <!-- products Section -->
      <section class="-mt-[85vh]">
        <h2 class="text-3xl font-semibold text-center mb-6">
          {{ texts.productsTitle }}
        </h2>
        <div class="grid md:grid-cols-4 gap-6 max-w-4xl mx-auto">
          <div
            v-for="(prod, i) in products"
            :key="i"
            class="relative w-52 h-52 max-w-sm overflow-hidden shadow-lg"
            :style="{ transitionDelay: `${150}ms`}"
          >
            <img :src="prod.img" :alt="prod.title" class="absolute inset-0 w-full h-full object-cover" />
            <div class="absolute inset-0 flex flex-col justify-end items-end text-white bg-black/30 px-4 pb-4">
              <button
                class="bg-white text-cyan-800 font-semibold px-4 py-1.5 rounded-xl hover:bg-cyan-100 hover:scale-110 transition-transform duration-300"
              >
                {{ texts.buy }}
              </button>
            </div>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

/* Language toggle */
const currentLang = ref("en");
const langData = {
  ka: {
    name: "ზვირთი",
    slogan: "შეივსე ბუნების ძალით",
    productsTitle: "პროდუქტები",
    prod1: "პროდუქტი 1",
    prod2: "პროდუქტი 2",
    prod3: "პროდუქტი 3",
    prod4: "პროდუქტი 4",
    prod5: "პროდუქტი 5",
    prod6: "პროდუქტი 6",
    prod7: "პროდუქტი 7",
    prod8: "პროდუქტი 8",
    buy: "შეძენა",
    footer: "© 2025 ეკატო მაყაშვილი. ყველა უფლება დაცულია.",
  },
  en: {
    name: "Zvirti",
    slogan: "Let nature fill you",
    productsTitle: "Products",
    prod1: "Product 1",
    prod2: "Product 2",
    prod2: "Product 2",
    prod3: "Product 3",
    prod4: "Product 4",
    prod5: "Product 5",
    prod6: "Product 6",
    prod7: "Product 7",
    prod8: "Product 8",

    buy: "Buy",
    footer: "© 2025 Zvirti. All rights reserved.",
  },
};

const shells = ref([
  "🫧",
  "🐬",
  "🪼",
  "🪸",
  "🦀",
  "𓇼",
]);

const waveY = ref(shells.value.map(() => 0));
const wavePath = ref(null);

let t = 0;
const amplitude = 15; // ტალღის სიმაღლე
const frequency = 0.002; // ტალღის სიგრძე
const speed = 0.01; // ტალღის სიჩქარე

function animateWave() {
  const waveWidth = 1440; // SVG width
  const waveHeight = 60; // base wave amplitude reference
  const amplitude = 15; // skill box + wave amplitude
  const speed = 0.01; // controls wave speed
  t += speed;

  // Animate the wave path
  if (wavePath.value) {
    const cp1 = 360;
    const cp2 = 1080;

    const newD = `
      M0,${30 + Math.sin(t) * amplitude} 
      C${cp1},${90 + Math.sin(t + 1) * amplitude} 
       ${cp2},${-30 + Math.sin(t + 2) * amplitude} 
       1440,${30 + Math.sin(t + 3) * amplitude} 
      L1440,150 L0,150 Z
    `;
    wavePath.value.setAttribute("d", newD);
  }

  // Animate skill boxes along same sine trajectory
  shells.value.forEach((_, i) => {
    // calculate X-position fraction for each skill box
    const fraction = i / (shells.value.length - 1);
    // map fraction to wave sine for exact sync
    const phase = fraction * Math.PI * 2;
    waveY.value[i] = Math.sin(t + phase) * amplitude;
  });

  requestAnimationFrame(animateWave);
}

onMounted(() => {
  animateWave();
});
const texts = computed(() => langData[currentLang.value]);
const toggleLanguage = () =>
  (currentLang.value = currentLang.value === "ka" ? "en" : "ka");

/* products */
const products = computed(() => [
  {
    img: "prod1.JPG",
    title: texts.value.prod1,
  },
  { img: "prod2.JPG", title: texts.value.prod2 },
  {
    img: "prod3.jpg",
    title: texts.value.prod3,
  },
    {
    img: "prod4.JPG",
    title: texts.value.prod4,
  },
    {
    img: "prod5.jpg",
    title: texts.value.prod5,
  },
    {
    img: "prod6.jpg",
    title: texts.value.prod6,
  },
    {
    img: "prod7.JPG",
    title: texts.value.prod7,
  },
    {
    img: "prod8.jpg",
    title: texts.value.prod8,
  },
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
/* Gradient animation */
@keyframes gradientShift {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}
.animate-gradient {
  background-size: 600% 600%;
  animation: gradientShift 20s ease infinite;
}

/* Particles rising */
@keyframes rise {
  0% {
    transform: translateY(0);
    opacity: 0.2;
  }
  50% {
    opacity: 0.3;
  }
  100% {
    transform: translateY(-120vh);
    opacity: 0;
  }
}
.shells-container {
  position: relative;
  z-index: 10;
}

.skill-box {
  background: rgba(255, 255, 255, 0.15);
  color: #e2dfd2;
  font-weight: 600;
  border-radius: 50px;
  padding: 8px 12px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 6px 18px rgba(14, 165, 233, 0.1);
  transition: transform 0.1s linear;
}

.wave-separator {
  bottom: 0;
  left: 0;
  width: 100%;
  height: 80vh;
}

/* Hover ეფექტი */
.skill-box:hover {
  transform: scale(1.08);
  box-shadow: 0 12px 30px rgba(14, 165, 233, 0.22);
  background: black;
  transition: 2s;
  opacity: 0.1;
}

/* სინუსოიდური ტალღა */
@keyframes wave {
  0% {
    transform: translateY(0);
  }
  25% {
    transform: translateY(-6px);
  }
  50% {
    transform: translateY(0);
  }
  75% {
    transform: translateY(6px);
  }
  100% {
    transform: translateY(0);
  }
}
</style>
