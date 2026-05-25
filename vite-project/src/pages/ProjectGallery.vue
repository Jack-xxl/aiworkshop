<template>
  <PageShell wide title="AI 作品展示墙" subtitle="营员们亲手创造的 AI 项目">
    <div v-if="loading" class="state-box panel">加载中…</div>
    <div v-else-if="projects.length === 0" class="state-box panel empty">
      <span class="empty-icon">🎨</span>
      <p>暂时还没有作品，快去创建一个吧！</p>
      <router-link class="btn btn-primary" to="/creator">去创造中心</router-link>
    </div>
    <div v-else class="grid">
      <div v-for="p in projects" :key="p.id" class="work-card" @click="goDetail(p.id)">
        <h2>{{ p.title }}</h2>
        <p class="meta">
          {{ mapType(p.type) }}
          <span v-if="p.subject"> · {{ p.subject }}</span>
        </p>
        <p class="summary">{{ p.summary }}</p>
        <p class="date">{{ formatDate(p.createdAt) }}</p>
      </div>
    </div>
  </PageShell>
</template>

<script setup>
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";
import PageShell from "../components/PageShell.vue";

const router = useRouter();
const projects = ref([]);
const loading = ref(false);

onMounted(loadProjects);

async function loadProjects() {
  loading.value = true;
  try {
    const res = await fetch("/api/projects");
    const data = await res.json();
    projects.value = Array.isArray(data)
      ? data.sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt))
      : [];
  } catch (e) {
    console.error(e);
  } finally {
    loading.value = false;
  }
}

function goDetail(id) {
  router.push({ name: "ProjectDetail", params: { id } });
}

function mapType(t) {
  if (t === "study-companion") return "AI 学习伴侣";
  if (t === "memory-system") return "AI 记忆系统";
  if (t === "goal-navigator") return "AI 目标导航员";
  return t;
}

function formatDate(d) {
  if (!d) return "";
  return new Date(d).toLocaleString();
}
</script>

<style scoped>
.state-box {
  text-align: center;
  padding: 40px;
  color: var(--text-muted);
}
.empty .empty-icon {
  font-size: 3rem;
  display: block;
  margin-bottom: 12px;
}
.empty p {
  margin-bottom: 20px;
}
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 16px;
}
.work-card {
  background: var(--bg-card);
  border: 1px solid var(--border);
  border-radius: var(--radius-lg);
  padding: 20px;
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease, border-color 0.3s;
  position: relative;
  overflow: hidden;
}
.work-card::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 3px;
  background: var(--gradient-main);
}
.work-card:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-lg);
  border-color: var(--border-hover);
  background: var(--bg-card-hover);
}
.work-card h2 {
  margin: 0 0 8px;
  font-size: 1.1rem;
  color: var(--text-primary);
}
.meta {
  color: var(--text-muted);
  font-size: 0.82rem;
  margin-bottom: 8px;
}
.summary {
  font-size: 0.92rem;
  margin-bottom: 8px;
  line-height: 1.55;
  color: var(--text);
}
.date {
  font-size: 0.78rem;
  color: var(--text-muted);
}
</style>
