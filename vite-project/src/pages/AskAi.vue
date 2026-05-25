<template>
  <PageShell
    title="AI 智能答疑"
    subtitle="仅限 Python、Scratch 与 AI 学习相关问题"
  >
    <div class="chat-panel panel">
      <div class="input-row">
        <input
          v-model="question"
          class="chat-input"
          placeholder="请输入与 Python / Scratch / AI 学习相关的问题"
          @keyup.enter="submit"
        />
        <select v-model="age" class="age-select">
          <option value="">年龄</option>
          <option v-for="a in ages" :key="a" :value="a">{{ a }}</option>
        </select>
        <button class="btn btn-primary" :disabled="loading" @click="submit">
          {{ loading ? "思考中…" : "提问" }}
        </button>
      </div>

      <p v-if="error" class="msg-err">{{ error }}</p>

      <div v-if="answer" class="answer-box">
        <div class="answer-label">AI 回答</div>
        <pre>{{ answer }}</pre>
      </div>

      <div v-else-if="!loading && !error" class="empty-hint">
        💬 输入问题后按回车或点击「提问」
      </div>
    </div>
  </PageShell>
</template>

<script setup>
import { ref } from "vue";
import PageShell from "../components/PageShell.vue";
import { askAi } from "../services/qwenService";

const question = ref("");
const age = ref("");
const answer = ref("");
const error = ref("");
const loading = ref(false);
const ages = [8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 25, 30];

async function submit() {
  answer.value = "";
  error.value = "";
  const q = question.value.trim();
  if (!q) {
    error.value = "请输入问题";
    return;
  }
  loading.value = true;
  try {
    const data = await askAi(q, age.value || undefined);
    answer.value = data?.answer ?? JSON.stringify(data);
  } catch (e) {
    console.error("[UI] submit error:", e);
    error.value = e?.response?.data?.error || e.message;
  } finally {
    loading.value = false;
  }
}
</script>

<style scoped>
.chat-panel {
  padding: 24px;
}
.input-row {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  align-items: stretch;
}
.chat-input {
  flex: 1;
  min-width: 200px;
  padding: 12px 16px;
  border: 1px solid var(--border);
  border-radius: 12px;
  font-family: var(--font);
  font-size: 1rem;
  background: var(--surface-2);
}
.chat-input:focus {
  outline: none;
  border-color: var(--accent-purple);
  box-shadow: 0 0 0 2px rgba(124, 58, 237, 0.35), 0 0 24px rgba(124, 58, 237, 0.2);
  background: var(--bg-primary);
}
.age-select {
  padding: 12px 14px;
  border: 1px solid var(--border);
  border-radius: 12px;
  font-family: var(--font);
  background: var(--bg-primary);
  color: var(--text-primary);
  min-width: 88px;
}
.answer-box {
  margin-top: 20px;
  padding: 18px;
  background: var(--surface-2);
  border-radius: var(--radius);
  border: 1px solid var(--border);
}
.answer-label {
  font-size: 0.8rem;
  font-weight: 700;
  color: var(--primary);
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 10px;
}
.answer-box pre {
  margin: 0;
  white-space: pre-wrap;
  font-family: var(--font);
  font-size: 0.95rem;
  line-height: 1.7;
  color: var(--text);
}
.empty-hint {
  margin-top: 24px;
  text-align: center;
  color: var(--text-muted);
  font-size: 0.95rem;
}
</style>
