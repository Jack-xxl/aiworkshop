<template>
  <PageShell wide :show-back="true" title="成长路线 · 课程体系" subtitle="三大阶段分龄培养 — 案例展示">
    <div class="cta-row">
      <a class="btn btn-primary" href="#plans">查看成长路线</a>
      <router-link class="btn btn-ghost" to="/showcase">浏览案例展示</router-link>
    </div>

    <section id="plans" class="section-block">
      <h2 class="section-title">🧩 成长路线 · 三大阶段</h2>
      <div class="plan-table">
        <div v-for="p in plans" :key="p.badge" class="plan panel">
          <span class="plan-badge">{{ p.badge }}</span>
          <div class="plan-title">{{ p.title }}</div>
          <ul class="plan-feats">
            <li v-for="f in p.features" :key="f">{{ f }}</li>
          </ul>
          <div v-for="d in p.durations" :key="d" class="duration" :class="{ sub: d.sub }">{{ d.text }}</div>
        </div>
      </div>
    </section>

    <section class="section-block">
      <h2 class="section-title">🎯 阶段亮点</h2>
      <div v-for="s in stages" :key="s.badge" class="stage panel">
        <div class="stage-badge">{{ s.badge }}</div>
        <ul>
          <li v-for="item in s.items" :key="item" v-html="item" />
          <li v-if="s.hint" class="hint">{{ s.hint }}</li>
        </ul>
      </div>
    </section>

    <section class="section-block">
      <h2 class="section-title">🚀 核心优势</h2>
      <div class="advantage-grid">
        <div v-for="a in advantages" :key="a.title" class="info-card">
          <h3>{{ a.title }}</h3>
          <p v-html="a.desc" />
        </div>
      </div>
      <p class="foot-note">让孩子成为 <strong>AI 时代的创造者</strong> — 抓住红利，不焦虑、不内卷。</p>
    </section>
  </PageShell>
</template>

<script setup>
import PageShell from "../components/PageShell.vue";

const plans = [
  {
    badge: "🟠 兴趣启蒙",
    title: "6–9岁 · 激发兴趣 · 建立逻辑思维",
    features: ["趣味视频", "每周 1 次线上思维课（45分钟）", "每月线下工坊（90分钟）"],
    durations: [{ text: "16 周 · 案例展示" }],
  },
  {
    badge: "🟡 项目实战",
    title: "10岁+ · 独立创作 · 解决真实问题 · 就业&创业启蒙",
    features: ["视频自学 + 每周线上思维课（60分钟）", "每周线下项目课（90分钟）"],
    durations: [{ text: "20 周 · 案例展示" }, { text: "40 周 · 进阶案例", sub: true }],
  },
  {
    badge: "🔵 实验室孵化",
    title: "优秀学员 · 企业对接 · 创业力培养 · 项目商业化",
    features: ["企业真实项目 + 路演实践 + 商业探索"],
    durations: [{ text: "半年 · 孵化案例" }, { text: "全年 · 深度孵化", sub: true }],
  },
];

const stages = [
  {
    badge: "🟠 阶段一 · 兴趣启蒙（6–9岁）",
    items: [
      "学习 <strong>Scratch 编程</strong> + AI 趣味工具",
      "在 <strong>玩中学</strong>，培养逻辑思维 · 想象力 · 创造力",
      "完成 2–3 个小作品，生成 <strong>AI 成长档案</strong>",
    ],
    hint: "🌱 成果：点燃兴趣，建立科技自信",
  },
  {
    badge: "🟡 阶段二 · 项目实战（10岁+）",
    items: [
      "系统学习 <strong>Python 编程</strong>",
      "发现天赋与热爱，选择领域做真实项目",
      "培养就业级能力：编程 · 项目管理 · 团队协作",
      "引入创业启蒙：需求调研 · 产品思维 · 路演表达",
    ],
    hint: "🌟 成果：个人作品集 + AI少年开发者认证",
  },
  {
    badge: "🔵 阶段三 · 实验室孵化",
    items: [
      "参与 <strong>企业真实需求项目</strong>",
      "学习产品设计 · 商业模式 · 路演演讲",
      "体验 <strong>创意 → 产品 → 商业化</strong> 闭环",
    ],
    hint: "🚀 成果：路演作品、实习机会、商业化探索",
  },
];

const advantages = [
  { title: "🌍 真实社会项目", desc: "基于现实问题，感受 AI 的真正价值。" },
  { title: "🧒 分龄学习体系", desc: "启蒙 → 实战 → 孵化，科学分层。" },
  { title: "👨‍🏫 导师陪伴成长", desc: "导师 + AI 助教双轨，全程跟进。" },
  { title: "👪 家长全程可见", desc: "进度、报告、档案透明化。" },
  { title: "🤝 团队协作", desc: "分工、站会、路演，锻炼软实力。" },
  { title: "💼 就业预备力", desc: "作品集 + 实战经验，更有底气。" },
  { title: "🚀 创业孵化", desc: "体验创造价值 → 收获回报的闭环。" },
  { title: "📈 长期发展导向", desc: "创业思维 + 独立谋生力。" },
];
</script>

<style scoped>
.cta-row {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  justify-content: center;
  margin-bottom: 32px;
}
.section-block {
  margin-bottom: 40px;
}
.section-title {
  font-size: 1.25rem;
  font-weight: 800;
  margin-bottom: 16px;
  color: var(--text-primary);
}
.plan-table {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
}
.plan {
  margin-bottom: 0;
}
.plan-badge {
  display: inline-block;
  padding: 4px 12px;
  background: var(--primary-soft);
  border-radius: 999px;
  font-weight: 700;
  font-size: 0.85rem;
  margin-bottom: 8px;
}
.plan-title {
  font-weight: 700;
  margin-bottom: 10px;
  line-height: 1.4;
  color: var(--text-primary);
}
.plan-feats {
  margin: 0 0 12px;
  padding-left: 18px;
  color: var(--text-muted);
  font-size: 0.92rem;
}
.duration {
  font-size: 0.9rem;
  font-weight: 700;
  color: var(--primary);
}
.duration.sub {
  margin-top: 4px;
  color: var(--accent);
  font-size: 0.85rem;
}
.stage ul {
  margin: 0;
  padding-left: 18px;
}
.stage-badge {
  font-weight: 800;
  margin-bottom: 8px;
  color: var(--text-primary);
}
.hint {
  color: var(--success);
  font-weight: 600;
  list-style: none;
  margin-left: -18px;
  padding-left: 0;
}
.advantage-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
}
.foot-note {
  text-align: center;
  color: var(--text-muted);
  margin-top: 20px;
}
@media (max-width: 960px) {
  .plan-table,
  .advantage-grid {
    grid-template-columns: 1fr 1fr;
  }
}
@media (max-width: 640px) {
  .plan-table,
  .advantage-grid {
    grid-template-columns: 1fr;
  }
}
</style>
