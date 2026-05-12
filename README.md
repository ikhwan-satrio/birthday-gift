# 💕 For Someone Special — Birthday Surprise Web App

A romantic, interactive birthday surprise website built with **Nuxt 4**, **Three.js**, and **TailwindCSS**. Features animated 3D floating hearts, multi-step reveal, confetti celebration, and full mobile responsiveness.

---

## ✨ Features

- 🎂 5-step interactive birthday reveal
- 💗 3D floating hearts background (Three.js)
- 🎊 Confetti celebration on the final step
- 📱 Fully responsive for all screen sizes
- 🌀 Smooth step transitions with GSAP-powered animations
- 🖼️ Custom photo slot for a personal memory

---

## 🛠️ Tech Stack

| Tech | Purpose |
|---|---|
| Nuxt 4 | Framework |
| TailwindCSS | Styling |
| Three.js | 3D heart background |
| GSAP | Animation |
| canvas-confetti | Confetti effect |

---

## 📦 Installation

```bash
# Clone the repo
git clone https://github.com/yourusername/for-someone-special.git
cd for-someone-special

# Install dependencies
pnpm install

# Start dev server
pnpm dev
```

---

## 🖼️ Add Your Photo

Place your photo in the `public/` folder and name it `crush.webp`:

```
public/
└── crush.webp   ← your photo here
```

Supported formats: `.webp`, `.jpg`, `.png`

---

## 📁 Project Structure

```
├── pages/
│   └── special.vue        # Main page
├── public/
│   └── crush.webp         # Your photo (add this yourself)
├── nuxt.config.ts
├── tailwind.config.ts
└── package.json
```

---

## ⚙️ Customization

Open `pages/special.vue` and edit these sections:

### Change the name / greeting
```vue
<h1 class="text-gradient">Hey Beautiful</h1>
```

### Change the reasons cards (Step 3)
```vue
<h3 class="text-pink-600">Your Kindness</h3>
<p>The way you care about others...</p>
```

### Change the final message (Step 5)
```vue
<p class="text-pink-600">Happy Birthday, my love! ❤️</p>
```

### Change number of floating hearts
```ts
for (let i = 0; i < 25; i++) {  // change 25 to any number
```

---

## 📜 Scripts

```bash
pnpm dev        # Start development server
pnpm build      # Build for production
pnpm preview    # Preview production build
```

---

## 📄 License

Made with ❤️ — free to use and customize for personal use.
