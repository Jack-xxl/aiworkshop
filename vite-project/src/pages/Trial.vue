<template>
  <PageShell title="预约体验" subtitle="感受 AI 创变营的学习方式与成长氛围">
    <div class="panel panel-accent">
      <h3>试听课你将收获</h3>
      <ul class="feature-list" style="margin-top:12px;">
        <li>真实体验 AI 项目式学习流程</li>
        <li>用 AI 工具解决小实际问题或制作作品雏形</li>
        <li>体验导师互动与 AI 智能辅助教学</li>
        <li>了解成长路线和晋级通道</li>
        <li>试听后可获个性化成长建议</li>
      </ul>
      <p class="tip">本页为案例展示，欢迎预约体验了解平台能力。</p>
    </div>

    <form class="form-panel" @submit.prevent="handleSubmit">
      <div class="form-field">
        <label>家长姓名</label>
        <input v-model="parentName" required />
      </div>
      <div class="form-field">
        <label>联系方式</label>
        <input v-model="contact" type="tel" required />
      </div>
      <div class="form-field">
        <label>孩子姓名</label>
        <input v-model="childName" />
      </div>
      <div class="form-field">
        <label>孩子年龄</label>
        <input v-model="age" type="number" min="4" max="22" />
      </div>
      <button type="submit" class="btn btn-success" style="width:100%;">提交预约</button>
    </form>
    <p v-if="submitted" class="msg-ok">🎉 预约成功！我们会尽快与您联系安排体验。</p>
  </PageShell>
</template>

<script setup>
import { ref } from "vue";
import PageShell from "../components/PageShell.vue";

const parentName = ref("");
const contact = ref("");
const childName = ref("");
const age = ref("");
const submitted = ref(false);

function handleSubmit() {
  submitted.value = true;
}
</script>

<style scoped>
.tip {
  margin: 12px 0 0;
  font-size: 0.9rem;
  color: var(--warn);
  font-weight: 600;
}
</style>
