<!-- vite-project/src/pages/ProjectDetail.vue -->
<template>
  <div class="page-wide" v-if="project">
    <router-link class="back-link" to="/gallery">← 返回作品墙</router-link>
    <h1 class="title">{{ project.title }}</h1>
    <p class="meta">
      类型：{{ mapType(project.type) }}
      <span v-if="project.subject"> · 学科：{{ project.subject }}</span>
    </p>
    <p class="meta">创作者：{{ project.author }} · 创建时间：{{ formatDate(project.createdAt) }}</p>

    <div class="block">
      <h2>项目目标</h2>
      <p>{{ project.summary }}</p>
    </div>

    <div class="block">
      <h2>AI 生成与孩子完善后的内容</h2>
      <pre>{{ project.content }}</pre>
    </div>

  </div>

  <div v-else class="page-wide state">作品加载中或不存在。</div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const project = ref(null);

onMounted(loadProject);

async function loadProject() {
  try {
    const res = await fetch(`/api/projects/${route.params.id}`);
    if (!res.ok) return;
    project.value = await res.json();
  } catch (e) {
    console.error(e);
  }
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
.title {
  font-size: 1.6rem;
  font-weight: 800;
  margin: 0 0 8px;
}
.meta {
  color: var(--text-muted);
  font-size: 0.88rem;
  margin-bottom: 4px;
}
.block {
  margin-top: 20px;
  padding: 20px;
  border-radius: var(--radius-lg);
  border: 1px solid var(--border);
  background: var(--surface);
  box-shadow: var(--shadow);
}
.block h2 {
  margin-top: 0;
  font-size: 1rem;
}
pre {
  white-space: pre-wrap;
  font-size: 0.9rem;
  line-height: 1.65;
  color: var(--text);
}
.state {
  color: var(--text-muted);
  text-align: center;
  padding: 48px;
}
</style>
