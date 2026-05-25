<template>
  <PageShell
    title="企业孵化与合作"
    subtitle="青少年创新项目的孵化器与社会实践入口 — 面向学员、家长、企业与投资人"
  >
    <div class="panel panel-accent animate-fade-up delay-3">
      <h3>项目孵化与创新支持</h3>
      <ul class="incubation-list" style="margin-top:16px;">
        <li v-for="(item, i) in supportItems" :key="i" class="incubation-item">
          <span class="incubation-icon">{{ item.icon }}</span>
          <span class="incubation-body"><b>{{ item.title }}</b> — {{ item.desc }}</span>
        </li>
      </ul>
    </div>

    <div class="panel animate-fade-up delay-4">
      <h3>合作企业与伙伴</h3>
      <div class="card-grid cols-2" style="margin-top:16px;">
        <div v-for="c in partners" :key="c.title" class="info-card">
          <div class="partner-icon">{{ c.icon }}</div>
          <h3>{{ c.title }}</h3>
          <p>{{ c.desc }}</p>
        </div>
      </div>
    </div>

    <div class="panel animate-fade-up delay-4">
      <h3>合作与孵化流程</h3>
      <ol class="steps">
        <li v-for="(s, i) in steps" :key="s">
          <span class="step-num">{{ i + 1 }}</span>
          {{ s }}
        </li>
      </ol>
    </div>

    <div class="panel animate-fade-up delay-5">
      <h3>愿景展望</h3>
      <p>
        我们致力于打造中国和全球青少年 AI 创新项目的最佳孵化平台，链接企业、投资人、导师和社会资源，帮助更多孩子将 AI 梦想转化为现实价值。
      </p>
    </div>
  </PageShell>
</template>

<script setup>
import PageShell from "../components/PageShell.vue";

const supportItems = [
  { icon: "🚀", title: "项目孵化支持", desc: "导师指导、平台资源与技术支持，有机会成为「少年 CEO」。" },
  { icon: "🎤", title: "创新路演与展示", desc: "定期 AI 创新路演，向企业/投资人展示项目。" },
  { icon: "💰", title: "孵化基金与奖励", desc: "优秀项目有机会获得创新奖金与创业启动资金。" },
  { icon: "📈", title: "平台投资与推广", desc: "特别优秀的项目，平台直接参与投资与产业合作。" },
  { icon: "💼", title: "实习与企业实践", desc: "推荐至合作企业，参与真实 AI 项目开发。" },
  { icon: "👨‍🏫", title: "专业导师陪伴", desc: "企业家、技术大咖、教育专家全程指导。" },
];

const partners = [
  { icon: "👥", title: "人才直通车", desc: "企业可优先对接平台优秀学员，储备未来 AI 人才。" },
  { icon: "🔬", title: "联合项目开发", desc: "校企联合研发，共同推进 AI 创新项目落地。" },
  { icon: "📣", title: "品牌共建", desc: "合作伙伴获得品牌展示与联合宣传机会。" },
  { icon: "🤝", title: "投资对接", desc: "欢迎投资人参与青少年创新项目，联合孵化共赢。" },
];

const steps = [
  "项目入驻与评审",
  "专家评审与团队组建",
  "项目孵化、导师陪伴与支持",
  "路演展示、企业与投资对接",
  "投资 / 实习 / 成果转化",
];
</script>

<style scoped>
.partner-icon {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  background: linear-gradient(135deg, rgba(124, 58, 237, 0.25), rgba(37, 99, 235, 0.2));
  font-size: 1.1rem;
  margin-bottom: 10px;
}
.steps {
  list-style: none;
  padding: 0;
  margin: 16px 0 0;
}
.steps li {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 14px 0;
  border-bottom: 1px solid var(--border);
  color: var(--text-secondary);
}
.steps li:last-child {
  border-bottom: none;
}
.step-num {
  flex-shrink: 0;
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--gradient-main);
  color: #fff;
  font-weight: 800;
  border-radius: 50%;
  font-size: 0.9rem;
}
</style>
