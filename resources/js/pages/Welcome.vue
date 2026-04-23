<script setup lang="ts">
import { dashboard, login, register } from '@/routes';
import { Head, Link } from '@inertiajs/vue3';
import { useAppearance } from '@/composables/useAppearance'; // ← sesuaikan path
import AppLogoIcon from '@/components/AppLogoIcon.vue';
import { Sun, Moon, SpellCheck, ChevronDown, Expand, Shrink } from 'lucide-vue-next'; // jika pakai lucide icon (disarankan)
import { ref, onMounted, onUnmounted, computed } from 'vue';

const { appearance, updateAppearance } = useAppearance();

const shopUrl = computed(() => {
  const slug = localStorage.getItem('ordernow_selected_store_slug');
  return slug ? `/@${slug}` : '/@dapatin-pusat';
});


function toggleAppearance() {
  const current = appearance.value;

  const next =
    current === 'system'
      ? 'light'
      : current === 'light'
      ? 'dark'
      : 'system';

  updateAppearance(next);
}

// --- Fullscreen State ---
const isFullscreen = ref(false);

function getFullscreenStatus() {
  const doc: any = document;
  return !!(
    doc.fullscreenElement ||
    doc.webkitFullscreenElement ||
    doc.mozFullScreenElement ||
    doc.msFullscreenElement
  );
}

function handleFullscreenChange() {
  isFullscreen.value = getFullscreenStatus();
}

function toggleFullscreen() {
  const doc: any = document;

  if (getFullscreenStatus()) {
    if (doc.exitFullscreen) doc.exitFullscreen();
    else if (doc.webkitExitFullscreen) doc.webkitExitFullscreen();
    else if (doc.mozCancelFullScreen) doc.mozCancelFullScreen();
    else if (doc.msExitFullscreen) doc.msExitFullscreen();
  } else {
    const el: any = document.documentElement;
    if (el.requestFullscreen) el.requestFullscreen();
    else if (el.webkitRequestFullscreen) el.webkitRequestFullscreen();
    else if (el.mozRequestFullScreen) el.mozRequestFullScreen();
    else if (el.msRequestFullscreen) el.msRequestFullscreen();
  }
}

onMounted(() => {
  document.addEventListener('fullscreenchange', handleFullscreenChange);
  document.addEventListener('webkitfullscreenchange', handleFullscreenChange);
  document.addEventListener('mozfullscreenchange', handleFullscreenChange);
  document.addEventListener('MSFullscreenChange', handleFullscreenChange);
  isFullscreen.value = getFullscreenStatus();
});

onUnmounted(() => {
  document.removeEventListener('fullscreenchange', handleFullscreenChange);
  document.removeEventListener('webkitfullscreenchange', handleFullscreenChange);
  document.removeEventListener('mozfullscreenchange', handleFullscreenChange);
  document.removeEventListener('MSFullscreenChange', handleFullscreenChange);
});


const activeFaq = ref<number | null>(null);

function toggleFaq(index: number) {
  activeFaq.value = activeFaq.value === index ? null : index;
}

// --- HERO AUTOPLAY TEXT ---
const heroTexts = [
  {
    html: `UD. <span class="text-yellow-300">TAWAKKAL</span>`,
  },
]

const activeHeroIndex = ref(0)
let heroInterval: number | undefined

onMounted(() => {
  heroInterval = window.setInterval(() => {
    activeHeroIndex.value =
      (activeHeroIndex.value + 1) % heroTexts.length
  }, 5000) // ganti tiap 3 detik
})

onUnmounted(() => {
  if (heroInterval) clearInterval(heroInterval)
})


</script>

<template>
  <Head title="Toko Online, Sistem Kasir, Akuntansi jadi satu">
    <link rel="preconnect" href="https://rsms.me/" />
    <link rel="stylesheet" href="https://rsms.me/inter/inter.css" />
  </Head>

  <!-- WRAPPER -->
  <div 
    class="min-h-screen flex flex-col text-[#1b1b18] dark:text-white 
           bg-linear-to-br from-blue-500 via-purple-500 to-red-500
           dark:bg-linear-to-br dark:from-[#0b0f2b] dark:via-[#2e0f3a] dark:to-[#3b0d0d]"
  >

    <!-- NAVBAR -->
    <header class="w-full px-6 py-4 flex justify-between items-center">
      <div class="flex flex-nowrap items-center">
        <span class="size-10"><AppLogoIcon /></span>
        <h1 class="text-2xl font-extrabold tracking-tight text-white drop-shadow ms-2">
          TAWAKKAL
        </h1>
      </div>

      <nav class="flex items-center gap-2.5 text-sm">

        <!-- tombol toggle appearance (system / light / dark) -->
        <button
          @click="toggleAppearance"
          class="p-1 rounded-full border border-white/40 bg-white/10 hover:bg-white/20 transition backdrop-blur"
        >
          <!-- system mode -->
          <SpellCheck v-if="appearance === 'system'" class="size-4 text-white" />

          <!-- light mode -->
          <Sun v-else-if="appearance === 'light'" class="size-4 text-white" />

          <!-- dark mode -->
          <Moon v-else class="size-4 text-white" />
        </button>    
        
        <!-- toggle fullscreen -->
        <button
          @click="toggleFullscreen"
          class="p-1 rounded-full border border-white/40 bg-white/10 hover:bg-white/20 transition backdrop-blur"
          :title="isFullscreen ? 'Keluar Fullscreen' : 'Masuk Fullscreen'"
        >
          <Shrink v-if="isFullscreen" class="size-4 text-white" />
          <Expand v-else class="size-4 text-white" />
        </button>        

        <Link
          v-if="$page.props.auth.user"
          :href="dashboard()"
          class="px-4 py-1.5 rounded-md bg-white/20 text-white backdrop-blur hover:bg-white/30 transition"
        >
          Menu
        </Link>

        <template v-else>
          <!-- <Link
            :href="login()"
            class="px-4 py-1.5 rounded-md bg-white/20 text-white backdrop-blur hover:bg-white/30 transition"
          >
            Log In
          </Link> -->
          <Link
            :href="dashboard()"
            class="px-4 py-1.5 rounded-md bg-white/20 text-white backdrop-blur hover:bg-white/30 transition"
          >
            Masuk
          </Link>
        </template>
      </nav>
    </header>

    <!-- HERO SECTION -->
    <section 
      class="flex flex-col items-center justify-center text-center px-6 py-20 flex-grow"
    >
      <h2
        class="text-5xl md:text-6xl font-extrabold text-white drop-shadow-xl leading-tight min-h-[5.5rem]"
      >
        <transition
          name="hero-fade"
          mode="out-in"
        >
          <span
            :key="activeHeroIndex"
            v-html="heroTexts[activeHeroIndex].html"
          />
        </transition>
      </h2>


      <p class="mt-4 text-lg md:text-xl text-white/90 max-w-xl">
        Bahan Alumunoium dan Kaca berkualitas dengan harga terjangkau. TAWAKKAL, solusi belanja cerdas untuk kebutuhan rumah dan bisnis Anda.
      </p>
    </section>


    <!-- FOOTER -->
    <footer class="text-center py-8 px-2 text-white/80 text-sm bg-accent/30">
      Info:
      <a
        href="#"
        target="_blank"
        class="hover:underline hover:text-white transition"
      >
        0888-8888-8888
      </a>
      • 2026 TAWAKKAL
    </footer>

  </div>
</template>

<style>
.hero-fade-enter-active,
.hero-fade-leave-active {
  transition: all 0.5s ease;
}
.hero-fade-enter-from {
  opacity: 0;
  transform: translateY(10px);
}
.hero-fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
</style>

