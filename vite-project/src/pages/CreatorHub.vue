<template>
  <PageShell wide title="AI 智能体创造中心" subtitle="选择你想做的 AI 项目，开始创造属于你的 AI 小伙伴">
    <div class="creator-grid">
      <div
        v-for="c in coreProjects"
        :key="c.type"
        class="creator-card"
        @click="goCreate(c.type)"
      >
        <span class="card-icon">{{ c.icon }}</span>
        <h2>{{ c.title }}</h2>
        <p>{{ c.desc }}</p>
      </div>
    </div>

    <router-link class="gallery-link animate-fade-up delay-3" to="/gallery">
      查看同学们的 AI 作品墙 →
    </router-link>

    <section class="feature-section panel animate-fade-up delay-4">
      <h2>📘 AI 单词机 · 我的专属词汇教练</h2>
      <p class="section-desc">
        从单词库 → 记忆方式 → AI 规划 → 语音闯关 → 错词复习，打造属于你的单词训练系统。
      </p>
      <button class="btn btn-primary" @click="goWordMachine">🚀 进入 AI 单词机</button>
    </section>

    <section class="feature-section panel panel-accent animate-fade-up delay-5">
      <h2>🎨 AI 小助手创作工坊</h2>
      <p class="section-desc">
        设定名字、头像、主题色、性格，创造独一无二的 AI 小伙伴。
      </p>
      <button class="btn btn-warn" @click="goAgentBuilder">✨ 创建我的 AI 小助手</button>
    </section>
  </PageShell>
</template>

<script setup>
import { useRouter } from "vue-router";
import PageShell from "../components/PageShell.vue";

const router = useRouter();

const coreProjects = [
  { type: "study-companion", icon: "📖", title: "AI 学习伴侣", desc: "语文 / 数学 / 英语任选，AI 帮你制定计划、出题、讲解难题。" },
  { type: "memory-system", icon: "🧠", title: "AI 记忆系统生成器", desc: "从知识点生成记忆卡片 + 自测题，提升学习效率。" },
  { type: "goal-navigator", icon: "🧭", title: "AI 目标导航员", desc: "根据兴趣与优势，绘制未来成长路线图。" },
];

function goCreate(type) {
  router.push({ name: "ProjectCreate", query: { type } });
}
function goWordMachine() {
  router.push("/word-builder");
}
function goAgentBuilder() {
  router.push("/agent-builder");
}
</script>

<style scoped>
.gallery-link {
  display: inline-block;
  margin-bottom: 32px;
  font-weight: 600;
  color: var(--accent-cyan);
}
.feature-section {
  text-align: center;
  margin-bottom: 24px;
}
.feature-section h2 {
  margin: 0 0 8px;
  font-size: 1.2rem;
  color: var(--text-primary);
}
.section-desc {
  max-width: 560px;
  margin: 0 auto 20px;
  color: var(--text-secondary);
  font-size: 0.95rem;
  line-height: 1.65;
}
</style>
