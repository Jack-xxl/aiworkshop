<template>
  <div class="home-page">
    <!-- Hero 全屏 -->
    <section class="hero">
      <div class="hero-orb hero-orb-1"></div>
      <div class="hero-orb hero-orb-2"></div>
      <div class="hero-orb hero-orb-3"></div>
      <div class="particles" aria-hidden="true">
        <span v-for="n in 12" :key="n" class="particle" :style="particleStyle(n)"></span>
      </div>

      <div class="hero-content container animate-fade-up">
        <div class="hero-badge">案例展示 · 青少年 AI 创新平台</div>
        <h1 class="hero-title text-gradient">AI创变营</h1>
        <p class="hero-sub">青少年 AI 创新孵化平台</p>
        <p class="hero-desc">
          让孩子动手用 AI 做项目，解决真实问题。拒绝内卷和焦虑，用能力与作品赢得未来。
        </p>
        <router-link class="btn-cta hero-cta" to="/creator">开始创造 →</router-link>
      </div>
    </section>

    <!-- 功能导航 -->
    <section class="section-nav container">
      <h2 class="section-title text-gradient animate-fade-up delay-1">探索平台</h2>
      <p class="section-lead animate-fade-up delay-2">
        从学习到创造，一站式 AI 成长体验
      </p>
      <div class="glass-grid">
        <router-link
          v-for="(item, i) in navItems"
          :key="item.to"
          :to="item.to"
          class="glass-card nav-card"
          :class="[`animate-fade-up`, `delay-${Math.min(i + 2, 5)}`, item.variant]"
        >
          <span class="nav-icon">{{ item.icon }}</span>
          <span class="nav-label">{{ item.label }}</span>
          <span v-if="item.hint" class="nav-hint">{{ item.hint }}</span>
        </router-link>
      </div>
    </section>

    <!-- 亮点 -->
    <section class="section-highlights container animate-fade-up delay-3">
      <ul class="highlights glass-card">
        <li v-for="h in highlights" :key="h">{{ h }}</li>
      </ul>
    </section>

    <footer class="home-footer container animate-fade-up delay-4">
      <p>AI创变营 · 青少年AI创新孵化平台 © 2024</p>
      <p class="footer-sub">官方公众号 · 联系我们</p>
    </footer>
  </div>
</template>

<script setup>
const navItems = [
  { to: "/faq", label: "核心优势", icon: "💡", variant: "" },
  { to: "/showcase", label: "成果与展示", icon: "🏆", variant: "" },
  { to: "/incubation", label: "企业孵化与合作", icon: "🤝", variant: "" },
  { to: "/ai-ask", label: "AI 智能答疑", icon: "🤖", variant: "accent", hint: "Python / Scratch" },
  { to: "/creator", label: "AI 智能体创造中心", icon: "✨", variant: "featured" },
  { to: "/gallery", label: "AI 作品展示墙", icon: "🖼️", variant: "" },
  { to: "/study-agent", label: "英语 AI 学习伙伴", icon: "📚", variant: "study" },
];

const highlights = [
  "从零基础到创新项目，AI 能力与作品双收",
  "导师 + AI 全程陪伴，学习不孤单",
  "优秀者直通企业实习、孵化与合作平台",
];

function particleStyle(n) {
  const left = (n * 8.3) % 100;
  const delay = (n * 0.4) % 5;
  const duration = 4 + (n % 4);
  const size = 4 + (n % 6);
  return {
    left: `${left}%`,
    animationDelay: `${delay}s`,
    animationDuration: `${duration}s`,
    width: `${size}px`,
    height: `${size}px`,
  };
}
</script>

<style scoped>
.home-page {
  margin-top: calc(-1 * var(--nav-height));
}

/* Hero */
.hero {
  position: relative;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  padding: calc(var(--nav-height) + 40px) 24px 80px;
}

.hero-orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  pointer-events: none;
  animation: pulse-glow 8s ease-in-out infinite;
}
.hero-orb-1 {
  width: 480px;
  height: 480px;
  background: rgba(124, 58, 237, 0.35);
  top: -120px;
  left: 50%;
  transform: translateX(-60%);
}
.hero-orb-2 {
  width: 360px;
  height: 360px;
  background: rgba(37, 99, 235, 0.25);
  bottom: 10%;
  right: -80px;
  animation-delay: 2s;
}
.hero-orb-3 {
  width: 280px;
  height: 280px;
  background: rgba(6, 182, 212, 0.2);
  bottom: 30%;
  left: -60px;
  animation-delay: 4s;
}

.particles {
  position: absolute;
  inset: 0;
  pointer-events: none;
}
.particle {
  position: absolute;
  bottom: -10px;
  background: rgba(124, 58, 237, 0.6);
  border-radius: 50%;
  animation: float 6s ease-in-out infinite;
  box-shadow: 0 0 12px rgba(124, 58, 237, 0.5);
}

.hero-content {
  position: relative;
  z-index: 2;
  text-align: center;
  max-width: 800px;
}

.hero-badge {
  display: inline-block;
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--accent-cyan);
  border: 1px solid rgba(6, 182, 212, 0.3);
  padding: 8px 18px;
  border-radius: 999px;
  margin-bottom: 24px;
  background: rgba(6, 182, 212, 0.08);
}

.hero-title {
  font-size: clamp(2.5rem, 8vw, 3.5rem);
  font-weight: 800;
  letter-spacing: -0.04em;
  margin: 0 0 8px;
  line-height: 1.1;
}

.hero-sub {
  font-size: clamp(1.1rem, 3vw, 1.35rem);
  font-weight: 600;
  color: var(--text-secondary);
  margin: 0 0 20px;
}

.hero-desc {
  font-size: 1.1rem;
  color: var(--text-secondary);
  line-height: 1.75;
  max-width: 560px;
  margin: 0 auto 32px;
}

.hero-cta {
  font-size: 1.05rem;
  padding: 14px 36px;
}

/* Sections */
.section-nav {
  padding-top: var(--section-gap);
  padding-bottom: var(--section-gap);
}

.section-title {
  font-size: clamp(1.5rem, 4vw, 2rem);
  font-weight: 800;
  text-align: center;
  margin: 0 0 12px;
}

.section-lead {
  text-align: center;
  color: var(--text-secondary);
  margin: 0 0 40px;
  font-size: 1.05rem;
}

.glass-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.nav-card {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 6px;
  padding: 22px 24px;
  text-decoration: none;
  color: var(--text-primary);
}
.nav-card.featured {
  grid-column: 1 / -1;
  background: rgba(124, 58, 237, 0.12);
}
.nav-card.accent {
  border-color: rgba(245, 158, 11, 0.2);
}
.nav-card.study {
  border-color: rgba(6, 182, 212, 0.2);
}

.nav-icon {
  font-size: 1.6rem;
}
.nav-label {
  font-weight: 700;
  font-size: 1rem;
}
.nav-hint {
  font-size: 0.78rem;
  color: var(--text-secondary);
}

.section-highlights {
  padding-bottom: 48px;
}
.highlights {
  list-style: none;
  padding: 28px 32px;
  margin: 0;
}
.highlights li {
  padding: 10px 0 10px 28px;
  position: relative;
  color: var(--text-secondary);
  font-size: 0.98rem;
}
.highlights li::before {
  content: "★";
  position: absolute;
  left: 0;
  color: var(--accent-purple);
}

.home-footer {
  padding-bottom: 64px;
  text-align: center;
  color: var(--text-secondary);
  font-size: 0.9rem;
}
.footer-sub {
  margin-top: 8px;
  opacity: 0.8;
}

@media (max-width: 768px) {
  .glass-grid {
    grid-template-columns: 1fr;
  }
  .nav-card.featured {
    grid-column: auto;
  }
  .hero-title {
    font-size: 2.5rem;
  }
}
</style>
