<script setup lang="ts">
import confetti from "canvas-confetti";

useHead({
  title: "For Someone Special ❤️",
  script: [
    {
      src: "https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js",
    },
    { src: "https://cdnjs.cloudflare.com/ajax/libs/gsap/3.11.4/gsap.min.js" },
  ],
});

const currentStep = ref(1);
const totalSteps = 5;
const canvasContainer = ref<HTMLDivElement>();

const progress = computed(
  () => ((currentStep.value - 1) / (totalSteps - 1)) * 100,
);

function nextStep(step: number) {
  currentStep.value = step;
}

function celebrate() {
  confetti({
    particleCount: 150,
    spread: 70,
    origin: { y: 0.6 },
    colors: ["#ff0000", "#ff69b4", "#ff1493", "#ffc0cb"],
  });
  setTimeout(() => {
    confetti({
      particleCount: 30,
      spread: 60,
      origin: { y: 0.5 },
      colors: ["#ff0000", "#ff69b4"],
    });
  }, 300);
  setTimeout(() => {
    confetti({
      particleCount: 20,
      angle: 60,
      spread: 55,
      origin: { x: 0 },
      colors: ["#ff0000", "#ff69b4"],
    });
    confetti({
      particleCount: 20,
      angle: 120,
      spread: 55,
      origin: { x: 1 },
      colors: ["#ff0000", "#ff69b4"],
    });
  }, 600);
}

onMounted(async () => {
  const THREE = (window as any).THREE;
  const gsap = (window as any).gsap;
  if (!THREE || !canvasContainer.value) return;

  const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  canvasContainer.value.appendChild(renderer.domElement);

  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000,
  );
  camera.position.z = 30;

  const x = 0,
    y = 0;
  const heartShape = new THREE.Shape();
  heartShape.moveTo(x + 0.5, y + 0.5);
  heartShape.bezierCurveTo(x + 0.5, y + 0.5, x + 1, y, x, y);
  heartShape.bezierCurveTo(x - 1, y, x - 1, y + 1.5, x - 1, y + 1.5);
  heartShape.bezierCurveTo(x - 1, y + 2.5, x + 0.5, y + 3.5, x + 0.5, y + 3.5);
  heartShape.bezierCurveTo(x + 0.5, y + 3.5, x + 2, y + 2.5, x + 2, y + 1.5);
  heartShape.bezierCurveTo(x + 2, y + 1.5, x + 2, y, x + 1, y);
  heartShape.bezierCurveTo(x + 0.5, y, x + 0.5, y + 0.5, x + 0.5, y + 0.5);

  const heartGeometry = new THREE.ExtrudeGeometry(heartShape, {
    depth: 0.5,
    bevelEnabled: true,
    bevelSegments: 3,
    bevelSize: 0.2,
    bevelThickness: 0.3,
  });

  const colors = [
    0xff6b6b, 0xff8e8e, 0xffb3b3, 0xffd8d8, 0xff9e9e, 0xffc1c1, 0xff6b8e,
    0xff8eb3,
  ];
  const hearts: any[] = [];

  for (let i = 0; i < 25; i++) {
    const material = new THREE.MeshPhongMaterial({
      color: colors[Math.floor(Math.random() * colors.length)],
      emissive: 0xff0000,
      emissiveIntensity: 0.1,
      shininess: 100,
      transparent: true,
      opacity: 0.9,
    });
    const heart = new THREE.Mesh(heartGeometry, material);
    heart.position.set(
      (Math.random() - 0.5) * 50,
      (Math.random() - 0.5) * 50,
      (Math.random() - 0.5) * 50,
    );
    const scale = Math.random() * 0.8 + 0.5;
    heart.scale.set(scale, scale, scale);
    heart.rotation.set(Math.random() * Math.PI, Math.random() * Math.PI, 0);
    heart.userData = {
      speedX: Math.random() * 0.02 - 0.01,
      speedY: Math.random() * 0.02 - 0.01,
      speedZ: Math.random() * 0.02 - 0.01,
      rotationSpeedX: Math.random() * 0.01,
      rotationSpeedY: Math.random() * 0.01,
      originalX: heart.position.x,
      originalY: heart.position.y,
      originalZ: heart.position.z,
      floatDistance: 2 + Math.random() * 3,
    };
    scene.add(heart);
    hearts.push(heart);
  }

  scene.add(new THREE.AmbientLight(0xffffff, 0.5));
  const dirLight = new THREE.DirectionalLight(0xffffff, 0.8);
  dirLight.position.set(1, 1, 1);
  scene.add(dirLight);

  function animate() {
    requestAnimationFrame(animate);
    hearts.forEach((heart) => {
      const time = Date.now() * 0.001;
      heart.position.x =
        heart.userData.originalX +
        Math.sin(time * heart.userData.speedX * 10) *
          heart.userData.floatDistance;
      heart.position.y =
        heart.userData.originalY +
        Math.sin(time * heart.userData.speedY * 10) *
          heart.userData.floatDistance;
      heart.position.z =
        heart.userData.originalZ +
        Math.sin(time * heart.userData.speedZ * 10) *
          heart.userData.floatDistance;
      heart.rotation.x += heart.userData.rotationSpeedX;
      heart.rotation.y += heart.userData.rotationSpeedY;
    });
    renderer.render(scene, camera);
  }
  animate();

  window.addEventListener("resize", () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });

  // expose hearts for celebrate button
  (window as any).__hearts = hearts;
  (window as any).__gsap = gsap;
});

function celebrateWithHearts() {
  celebrate();
  const hearts = (window as any).__hearts;
  const gsap = (window as any).__gsap;
  if (hearts && gsap) {
    hearts.forEach((heart: any) => {
      gsap.to(heart.position, {
        y: heart.position.y - 30,
        duration: 3,
        ease: "power1.out",
      });
      gsap.to(heart.material, { opacity: 0, duration: 3, ease: "power1.out" });
    });
  }
}
</script>

<template>
  <div class="page-wrapper">
    <div ref="canvasContainer" class="fixed inset-0 z-[1]" />

    <!-- Progress bar -->
    <div
      class="fixed top-4 left-1/2 -translate-x-1/2 w-4/5 max-w-sm h-1.5 bg-white/30 rounded-full z-[100]"
    >
      <div
        class="h-full rounded-full transition-all duration-500"
        style="background: linear-gradient(90deg, #ec4899, #f43f5e)"
        :style="{ width: `${progress}%` }"
      />
    </div>

    <div
      class="fixed inset-0 z-10 flex items-center justify-center p-4 py-16 overflow-y-auto"
    >
      <div class="relative w-full flex justify-center">
        <Transition name="step" mode="out-in">
          <div v-if="currentStep === 1" key="1" class="step-card">
            <span class="emoji animate-bounce">❤️</span>
            <h1
              class="text-3xl sm:text-4xl md:text-5xl font-bold mb-4 text-gradient"
            >
              Hey Beautiful
            </h1>
            <p class="text-base sm:text-xl text-gray-700 mb-6">
              I made something special for you...
            </p>
            <button class="btn" @click="nextStep(2)">Let's Begin</button>
          </div>

          <div v-else-if="currentStep === 2" key="2" class="step-card">
            <span class="emoji">🎉</span>
            <h2
              class="text-2xl sm:text-3xl md:text-4xl font-bold mb-4 text-gradient"
            >
              Happy Birthday!
            </h2>
            <p class="text-sm sm:text-lg text-gray-700 mb-6">
              On your special day, I want you to know how amazing you are. Your
              smile brightens my world, your laughter is my favorite melody, and
              your presence makes every moment magical.
            </p>
            <button class="btn" @click="nextStep(3)">
              What makes you special?
            </button>
          </div>

          <div
            v-else-if="currentStep === 3"
            key="3"
            class="step-card"
            style="max-width: min(95vw, 800px)"
          >
            <h2 class="text-2xl sm:text-3xl font-bold mb-6 text-gradient">
              Here's why you're incredible
            </h2>
            <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 mb-6">
              <div class="reason-card">
                <div class="text-3xl mb-2">✨</div>
                <h3 class="text-base font-bold mb-1 text-pink-600">
                  Your Kindness
                </h3>
                <p class="text-xs sm:text-sm text-gray-700">
                  The way you care about others is truly inspiring.
                </p>
              </div>
              <div class="reason-card">
                <div class="text-3xl mb-2">😊</div>
                <h3 class="text-base font-bold mb-1 text-pink-600">
                  Your Smile
                </h3>
                <p class="text-xs sm:text-sm text-gray-700">
                  It lights up every room you enter.
                </p>
              </div>
              <div class="reason-card">
                <div class="text-3xl mb-2">🌟</div>
                <h3 class="text-base font-bold mb-1 text-pink-600">
                  Your Spirit
                </h3>
                <p class="text-xs sm:text-sm text-gray-700">
                  Your passion and energy are contagious!
                </p>
              </div>
            </div>
            <button class="btn" @click="nextStep(4)">A Little Surprise</button>
          </div>

          <div v-else-if="currentStep === 4" key="4" class="step-card">
            <span class="emoji">📸</span>
            <h2 class="text-2xl sm:text-3xl font-bold mb-4 text-gradient">
              Remember When...
            </h2>
            <div
              class="bg-gradient-to-r from-pink-200 to-rose-200 rounded-xl p-4 mb-4 flex flex-col items-center"
            >
              <img
                src="/memory.jpg"
                class="w-32 h-32 sm:w-44 sm:h-44 object-cover rounded-xl"
                alt="Our memory"
              />
              <p class="text-sm text-gray-700 italic mt-2">
                Our favorite memory together 💕
              </p>
            </div>
            <p class="text-sm sm:text-base text-gray-700 mb-4">
              Every moment with you becomes a cherished memory. Time with you is
              always special.
            </p>
            <button class="btn" @click="nextStep(5)">One Last Thing</button>
          </div>

          <div v-else-if="currentStep === 5" key="5" class="step-card">
            <span class="emoji">🎂</span>
            <h2 class="text-2xl sm:text-3xl font-bold mb-4 text-gradient">
              My Birthday Wish For You
            </h2>
            <p class="text-sm sm:text-base text-gray-700 mb-4">
              May your year be filled with joy, laughter, and dreams come true.
              May you always know how special you are to me and to everyone
              lucky enough to know you.
            </p>
            <p class="text-base sm:text-lg font-semibold text-pink-600 mb-4">
              Happy Birthday, my love! ❤️
            </p>
            <button class="btn" @click="celebrateWithHearts">
              Celebrate! 🎊
            </button>
            <p class="mt-4 text-xs text-gray-500">Made with ❤️ just for you</p>
          </div>
        </Transition>
      </div>
    </div>
  </div>
</template>
