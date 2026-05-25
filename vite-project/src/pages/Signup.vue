<template>
  <PageShell title="案例咨询" subtitle="了解 AI 创变营平台能力与成长路线">
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
      <div class="form-field">
        <label>课程意向</label>
        <select v-model="intention">
          <option value="AI兴趣启蒙（6-9岁）">AI兴趣启蒙（6-9岁）</option>
          <option value="AI项目实战（10岁及以上）">AI项目实战（10岁及以上）</option>
          <option value="AI少年实验室（高阶晋级）">AI少年实验室（高阶晋级）</option>
        </select>
      </div>
      <div class="form-field">
        <label>留言（可选）</label>
        <textarea v-model="msg" />
      </div>
      <button type="submit" class="btn btn-primary" style="width:100%;">提交咨询</button>
    </form>
    <p v-if="submitted" class="msg-ok">
      🎉 提交成功！我们将在 24 小时内联系您。
    </p>
  </PageShell>
</template>

<script setup>
import { ref } from "vue";
import PageShell from "../components/PageShell.vue";

const parentName = ref("");
const contact = ref("");
const childName = ref("");
const age = ref("");
const intention = ref("AI兴趣启蒙（6-9岁）");
const msg = ref("");
const submitted = ref(false);

function handleSubmit() {
  submitted.value = true;
}
</script>
