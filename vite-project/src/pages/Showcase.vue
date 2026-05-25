<template>
  <PageShell
    title="成果与展示"
    subtitle="主打 AI 产品、创新项目与未来成果墙 — 案例展示"
  >
    <div class="panel panel-accent">
      <h3>AI创变营 · 主打平台与创新项目</h3>
      <div class="card-grid cols-2" style="margin-top:16px;">
        <div v-for="p in products" :key="p.name" class="info-card">
          <h3>{{ p.icon }} {{ p.name }}</h3>
          <p>{{ p.desc }}</p>
        </div>
      </div>
    </div>

    <div class="panel">
      <h3>未来成果墙 · 等你来点亮</h3>
      <ul class="feature-list" style="margin-top:12px;">
        <li>AI 创新项目展示 — 学员与团队的 AI 作品与实践应用</li>
        <li>企业合作案例 — 企业项目、孵化案例、实习与创业故事</li>
        <li>导师点评与证书荣誉 — 优胜者获得认证、奖金与荣誉证书</li>
      </ul>
      <p class="muted">第一届 AI 创新营和企业孵化项目即将启动，成果墙等你来点亮！</p>
    </div>

    <div class="panel">
      <h3>团队亮点与平台愿景</h3>
      <p>
        我们是一支跨界 AI 专家和教育创新者团队，致力于用 AI 赋能青少年成长，打造世界一流的 AI 人才孵化平台。欢迎关注最新进展，也欢迎合作与加入！
      </p>
    </div>
  </PageShell>
</template>

<script setup>
import PageShell from "../components/PageShell.vue";

const products = [
  {
    name: "AI创变营",
    icon: "🚀",
    desc: "青少年 AI 创新孵化平台，提供素养培养、项目实战、创业孵化与社会能力全方位提升。",
  },
  {
    name: "TalentAI（天赋星图）",
    icon: "⭐",
    desc: "智能天赋测评，帮助孩子科学发现优势，精准规划未来发展路径。",
  },
  {
    name: "ParentNav（父母罗盘）",
    icon: "🧭",
    desc: "家长智能教养助手，赋能科学育儿，陪伴孩子共同成长。",
  },
  {
    name: "口语全球通",
    icon: "🌐",
    desc: "AI 驱动的英语口语训练，助力青少年自信沟通、国际化发展。",
  },
  {
    name: "EduNav",
    icon: "🎓",
    desc: "智能留学/游学/升学平台，AI 一键规划最适合的升学方案。",
  },
];
</script>

<style scoped>
.muted {
  margin-top: 12px;
  color: var(--text-muted);
  font-size: 0.92rem;
}
</style>
