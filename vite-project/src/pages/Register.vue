<template>
  <PageShell title="注册新账号" subtitle="创建账号以保存你的 AI 项目与学习进度" :show-back="true">
    <div class="form-panel">
      <div class="form-field">
        <label>邮箱</label>
        <input v-model="email" type="email" placeholder="输入邮箱" />
      </div>
      <div class="form-field">
        <label>密码</label>
        <input v-model="password" type="password" placeholder="输入密码" />
      </div>
      <button class="btn btn-primary" style="width:100%;" @click="handleRegister">注册</button>
      <p class="tip">
        已有账号？<router-link to="/">进入首页</router-link>
      </p>
      <p v-if="message" :class="message.startsWith('✅') ? 'msg-ok' : 'msg-err'">{{ message }}</p>
    </div>
  </PageShell>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import PageShell from "../components/PageShell.vue";

const email = ref("");
const password = ref("");
const message = ref("");
const router = useRouter();

function handleRegister() {
  if (!email.value || !password.value) {
    message.value = "❌ 邮箱和密码不能为空。";
    return;
  }
  const raw = localStorage.getItem("user-db");
  const db = raw ? JSON.parse(raw) : {};
  if (db[email.value]) {
    message.value = "❌ 该邮箱已注册。";
    return;
  }
  db[email.value] = { email: email.value, password: password.value };
  localStorage.setItem("user-db", JSON.stringify(db));
  message.value = "✅ 注册成功！正在进入首页...";
  setTimeout(() => router.push("/"), 800);
}
</script>

<style scoped>
.tip {
  margin-top: 16px;
  text-align: center;
  font-size: 0.9rem;
  color: var(--text-muted);
}
</style>
