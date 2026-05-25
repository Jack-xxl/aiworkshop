<template>
  <header class="app-nav" :class="{ scrolled: isScrolled, 'menu-open': menuOpen }">
    <div class="nav-inner container">
      <router-link class="nav-logo" to="/" @click="closeMenu">
        <span class="logo-mark">AI</span>
        <span class="logo-text">创变营</span>
      </router-link>

      <!-- 仅移动端显示 -->
      <button
        class="nav-toggle"
        type="button"
        aria-label="菜单"
        aria-expanded="menuOpen"
        @click="menuOpen = !menuOpen"
      >
        <span></span>
        <span></span>
        <span></span>
      </button>

      <div class="nav-menu">
        <nav class="nav-links">
          <router-link
            v-for="link in links"
            :key="link.to"
            :to="link.to"
            class="nav-link"
            @click="closeMenu"
          >
            {{ link.label }}
          </router-link>
        </nav>
        <router-link class="btn-cta nav-cta" to="/creator" @click="closeMenu">
          立即加入
        </router-link>
      </div>
    </div>
  </header>
</template>

<script setup>
import { onMounted, onUnmounted, ref } from "vue";

const isScrolled = ref(false);
const menuOpen = ref(false);

const links = [
  { to: "/faq", label: "核心优势" },
  { to: "/showcase", label: "成果展示" },
  { to: "/incubation", label: "企业孵化" },
  { to: "/ai-ask", label: "AI 答疑" },
  { to: "/creator", label: "创造中心" },
];

function onScroll() {
  isScrolled.value = window.scrollY > 8;
}

function closeMenu() {
  menuOpen.value = false;
}

onMounted(() => {
  window.addEventListener("scroll", onScroll, { passive: true });
  onScroll();
});
onUnmounted(() => {
  window.removeEventListener("scroll", onScroll);
});
</script>

<style scoped>
.app-nav {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  height: var(--nav-height);
  background: rgba(10, 10, 15, 0.8);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-bottom: 1px solid transparent;
  transition: border-color 0.3s ease, background 0.3s ease;
}
.app-nav.scrolled {
  border-bottom-color: var(--border);
}

.nav-inner {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100%;
  max-width: var(--max-content);
  margin: 0 auto;
  padding: 0 24px;
}

.nav-logo {
  display: flex;
  align-items: center;
  gap: 10px;
  text-decoration: none;
  color: var(--text-primary);
  font-weight: 800;
  font-size: 1.15rem;
  flex-shrink: 0;
}
.logo-mark {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  border-radius: 10px;
  background: var(--gradient-main);
  font-size: 0.75rem;
  font-weight: 800;
  color: #fff;
}
.logo-text {
  letter-spacing: -0.02em;
}

/* 桌面端：横排直接显示 */
.nav-toggle {
  display: none;
}

.nav-menu {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  flex: 1;
  gap: 32px;
  margin-left: 32px;
}

.nav-links {
  display: flex;
  align-items: center;
  gap: 32px;
}

.nav-link {
  font-size: 15px;
  font-weight: 500;
  color: #cbd5e1;
  text-decoration: none;
  white-space: nowrap;
  transition: color 0.2s ease;
  padding: 0;
  border-radius: 0;
  background: none;
}
.nav-link:hover,
.nav-link.router-link-active {
  color: #ffffff;
  background: none;
}

.nav-cta {
  flex-shrink: 0;
  font-size: 15px;
  padding: 10px 22px;
}

/* 移动端：汉堡菜单 + 折叠 */
@media (max-width: 768px) {
  .nav-inner {
    padding: 0 20px;
  }

  .nav-toggle {
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 5px;
    width: 40px;
    height: 40px;
    padding: 8px;
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 10px;
    cursor: pointer;
    flex-shrink: 0;
    margin-left: auto;
  }
  .nav-toggle span {
    display: block;
    height: 2px;
    background: var(--text-primary);
    border-radius: 2px;
    transition: transform 0.3s, opacity 0.3s;
  }

  .nav-menu {
    position: fixed;
    top: var(--nav-height);
    left: 0;
    right: 0;
    flex: none;
    flex-direction: column;
    align-items: stretch;
    gap: 4px;
    margin-left: 0;
    padding: 16px 24px 24px;
    background: rgba(10, 10, 15, 0.95);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border-bottom: 1px solid var(--border);
    transform: translateY(-100%);
    opacity: 0;
    visibility: hidden;
    transition: transform 0.3s ease, opacity 0.3s ease, visibility 0.3s;
    pointer-events: none;
  }

  .app-nav.menu-open .nav-menu {
    transform: translateY(0);
    opacity: 1;
    visibility: visible;
    pointer-events: auto;
  }

  .nav-links {
    flex-direction: column;
    align-items: stretch;
    gap: 4px;
  }

  .nav-link {
    padding: 14px 16px;
    font-size: 15px;
  }

  .nav-cta {
    margin-top: 8px;
    text-align: center;
    width: 100%;
  }

  .app-nav.menu-open .nav-toggle span:nth-child(1) {
    transform: translateY(7px) rotate(45deg);
  }
  .app-nav.menu-open .nav-toggle span:nth-child(2) {
    opacity: 0;
  }
  .app-nav.menu-open .nav-toggle span:nth-child(3) {
    transform: translateY(-7px) rotate(-45deg);
  }
}
</style>
