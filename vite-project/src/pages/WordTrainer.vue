<!-- src/pages/WordTrainer.vue -->
<template>

  <div class="trainer-page" :style="rootStyle">
    <router-link class="back-link trainer-back" to="/creator">← 返回创造中心</router-link>
    <header class="topbar">
      <!-- 顶部导师卡片 -->
      <div v-if="agentProfile" class="mentor-box" :class="themeClass">
        <div class="mentor-avatar">
          <span class="mentor-avatar-emoji">
            {{ mentorAvatarEmojiInitial }}
          </span>
        </div>
        <div class="mentor-meta">
          <div class="mentor-name">
            {{ agentProfile.mentorName || "我的单词教练" }}
          </div>
          <div class="mentor-desc">
            单词训练专属导师
            <span v-if="medalName">｜ 勋章：{{ medalName }}</span>
          </div>
          <div v-if="mentorSlogan" class="mentor-slogan">
            {{ mentorSlogan }}
          </div>
        </div>
      </div>

      <h1>今日单词记忆 · 训练中心</h1>
      <p v-if="config" class="sub">
        模式：{{ templateName }} ｜ 学习强度：{{ intensityLabel }} ｜ 复习流派：{{ reviewLabel }}
      </p>
      <p v-else class="sub warn">
        还没有配置 AI 单词机，请先在「AI 单词机 · 配置向导」里生成一套设置。
      </p>
    </header>
    <!-- 🔧 Playground 入口：从训练页跳到配置实验室 -->
<div class="trainer-nav-row" v-if="config">
  <router-link class="btn trainer-nav-btn" to="/playground">
    ⚙️ 打开 Playground，定制我的单词机（语速 / 颜色 / 按钮文案等）
  </router-link>
</div>

    <!-- 未配置时的引导 -->
    <main v-if="!config" class="empty">
      <router-link class="btn" to="/word-builder">
        去配置我的 AI 单词机 →
      </router-link>
    </main>

    <!-- 已配置：主训练界面 -->
    <main v-else class="content">
      <!-- 左侧：今日任务 + 单词卡片 -->
      <section class="left">
        <div class="progress-card">
          <div>今日任务进度</div>
          <p class="progress-num">
            {{ currentIndex + 1 > totalToday ? totalToday : currentIndex + 1 }}/{{ totalToday }}
          </p>
          <p class="small">
            已掌握：{{ masteredCount }} 个 ｜ 待巩固：{{ hardCount }} 个
          </p>
        </div>

        <div v-if="currentWord" class="word-card">
          <div class="word-main">
            <div class="word-text">{{ currentWord.text }}</div>

            <!-- 音标是否显示由 cardConfig 控制 -->
            <div
              class="word-phonetic"
              v-if="cardConfig.showPhonetic && currentWord.phonetic"
            >
              [{{ currentWord.phonetic }}]
            </div>

            <div class="word-meaning">
              {{ currentWord.meaning }}
            </div>

            <!-- 🔊 发音行：播放单词 + 语速选择 -->
            <div class="pronounce-row">
              <button class="btn-sm ghost" @click="playWordSound">
                🔊 播放单词
              </button>
              <button
                v-if="currentWord.examples?.length"
                class="btn-sm ghost"
                @click="playExampleSentence"
              >
                🔊 播放例句
              </button>
              <label class="small rate-label">
                语速：
                <select v-model.number="speechRate">
                  <option :value="0.5">慢 (0.5x)</option>
                  <option :value="0.75">较慢 (0.75x)</option>
                  <option :value="1">正常 (1.0x)</option>
                </select>
              </label>
            </div>
          </div>

          <!-- 例句：由 cardConfig.withExample 控制 -->
          <div
            v-if="cardConfig.withExample && currentWord.examples?.length"
            class="examples"
          >
            <div class="ex-title">例句</div>
            <ul>
              <li v-for="(ex, i) in currentWord.examples" :key="i">
                {{ ex }}
              </li>
            </ul>
          </div>

          <!-- 学生自己的记忆法：由 cardConfig.allowNote 控制 -->
          <div v-if="cardConfig.allowNote" class="note-block">
            <label class="note-label">我自己的联想 / 记忆法</label>
            <textarea
              v-model="currentWord.note"
              class="note-text"
              placeholder="比如：这个单词让我想到……（会保存在本地，下次出现还会看到）"
              @blur="saveWords"
            ></textarea>
          </div>

          <!-- 当前单词 AI 辅助区：举一反三 -->
          <div class="ai-panel">
            <button
              class="btn-sm ghost"
              @click="loadWordFamily"
              :disabled="loadingFamily"
            >
              {{ loadingFamily ? "AI 正在分析…" : "举一反三：词族 / 近义词 / 反义词" }}
            </button>
            <div class="ai-result" v-if="wordFamilyText">
              <div v-html="wordFamilyText"></div>
            </div>
            <div v-else class="ai-hint">
              点一下上面的按钮，AI 会帮你把这个单词的「家族关系」梳理出来。
            </div>
          </div>

          <!-- 按钮区：不会 / 认识 / 删除 -->
          <div class="actions">
            <button class="btn-secondary" @click="markHard">
              {{ buttonLabels.hardLabel }}
            </button>
            <button class="btn-primary" @click="markMastered">
              {{ buttonLabels.masteredLabel }}
            </button>
            <button class="btn-warning" @click="deleteCurrentWord">
              ❌ 删除这个单词
            </button>
          </div>
        </div>

        <div v-else class="finished-card">
          <h2>🎉 今日任务已完成！</h2>
          <p>你可以：</p>
          <ul>
            <li>再随机复习一轮今天的单词</li>
            <li>或者明天按 AI 计划继续走艾宾浩斯复习曲线</li>
          </ul>
          <button class="btn" @click="restartToday">再来一轮今天的单词</button>
        </div>
      </section>

      <!-- 右侧：语音闯关 + 错词挑战 + 故事/测验 + 学习小结 + 导入单词 -->
      <section class="right">
        <!-- 功能开关：让孩子自己选要不要这些模块 -->
        <div class="panel">
          <h2>学习助手开关</h2>
          <p class="small">你可以自己决定这几个 AI 小帮手要不要出现：</p>
          <label class="toggle">
            <input type="checkbox" v-model="prefs.showStory" @change="savePrefs" />
            <span>开启「错词故事工厂」</span>
          </label>
          <label class="toggle">
            <input type="checkbox" v-model="prefs.showQuiz" @change="savePrefs" />
            <span>开启「错词小测验」</span>
          </label>
          <label class="toggle">
            <input
              type="checkbox"
              v-model="prefs.showSummary"
              @change="savePrefs"
            />
            <span>开启「学习小结 & 鼓励语」</span>
          </label>
        </div>

        <!-- 语音闯关提示 -->
        <div class="panel">
          <h2>语音闯关（发音练习）</h2>
          <p v-if="config.voiceMode === 'none'" class="small">
            你在配置里关闭了语音闯关。如需开启，请回到「AI 单词机配置向导」修改设置。
          </p>
          <div v-else>
            <p class="small">
              当前难度：{{ voiceLabel }}。简单玩法：大声读出当前单词，再去「英语 Lily
              助手」里录音，让 AI 打分、纠正发音。
            </p>
            <router-link class="btn-sm" to="/study-agent">
              去「英语 Lily 助手」练发音 →
            </router-link>
          </div>
        </div>

        <!-- 错词挑战包 -->
        <div class="panel">
          <h2>错词挑战包</h2>
          <p class="small">
            下面是你最近记错过的单词（掌握度 &lt; 2）。点击一个可以直接跳到这个单词进行强化。
          </p>
          <div v-if="hardWords.length === 0" class="empty-box">
            暂无错词，继续保持！💪
          </div>
          <ul v-else class="hard-list">
            <li
              v-for="w in hardWords"
              :key="w.id"
              @click="jumpToWord(w.id)"
            >
              <span class="w-text">{{ w.text }}</span>
              <span class="w-meta">掌握度：{{ w.mastery }}</span>
            </li>
          </ul>
        </div>

        <!-- 错词故事工厂（可选） -->
        <div v-if="prefs.showStory" class="panel">
          <h2>错词故事工厂</h2>
          <p class="small">
            先把上面的「错词挑战包」多做几次，如果有一些单词老是记不住，可以让 AI
            把这些错词编进一个小故事里，帮你一起记。
          </p>
          <div class="story-controls">
            <label class="small">
              故事类型：
              <select v-model="storyStyle">
                <option value="funny">幽默搞笑</option>
                <option value="mystery">悬疑 / 小惊悚</option>
                <option value="scifi">科幻 / 未来感</option>
                <option value="adventure">冒险</option>
                <option value="slice_of_life">日常生活</option>
              </select>
            </label>
            <label class="small">
              长度：
              <select v-model="storyLength">
                <option value="short">短篇（30-120字）</option>
                <option value="medium">中等（150-250字）</option>
                <option value="long">长一点（300-400字）</option>
              </select>
            </label>
            <label class="small">
              口吻：
              <select v-model="storyTone">
                <option value="teacher">老师鼓励型</option>
                <option value="friend">朋友聊天型</option>
                <option value="hero">冒险英雄型</option>
              </select>
            </label>
          </div>
          <button
            class="btn-sm"
            @click="makeStoryFromHardWords"
            :disabled="loadingStory"
          >
            {{ loadingStory ? "AI 正在编故事…" : "用错词生成英文故事" }}
          </button>
          <div class="ai-result" v-if="storyText">
            <div v-html="storyText"></div>
          </div>
          <div v-else class="ai-hint">
            还没有故事内容。先做几道题，把老记错的单词积累起来，再点按钮试试看。
          </div>
        </div>

        <!-- 错词小测验（可选） -->
        <div v-if="prefs.showQuiz" class="panel">
          <h2>错词小测验</h2>
          <p class="small">
            用你最近出错的单词生成一份 3~5 题的小测验，检验一下是否真的记住了。
          </p>
          <button
            class="btn-sm"
            @click="makeQuizFromHardWords"
            :disabled="loadingQuiz"
          >
            {{ loadingQuiz ? "AI 正在出题…" : "生成一份错词小测验" }}
          </button>
          <div class="ai-result" v-if="quizText">
            <div v-html="quizText"></div>
          </div>
          <div v-else class="ai-hint">
            还没有测验内容。可以先点上面的按钮生成一份。
          </div>
        </div>

        <!-- 学习小结 + 鼓励语（可选） -->
        <div v-if="prefs.showSummary" class="panel">
          <h2>今天的学习小结</h2>
          <p class="small">
            每次完成任务后，点下面按钮，让 AI 用「老师口吻」给你写一段今天的学习反馈和鼓励。
          </p>
          <button
            class="btn-sm"
            @click="makeStudySummary"
            :disabled="loadingSummary"
          >
            {{ loadingSummary ? "AI 正在写给你的反馈…" : "生成今天的学习小结" }}
          </button>
          <div class="ai-result" v-if="summaryText">
            <div v-html="summaryText"></div>
          </div>
          <div v-else class="ai-hint">
            还没有小结内容。完成一轮任务后可以点按钮看看老师怎么评价你今天的表现。
          </div>
        </div>

        <!-- 导入单词（接入 AI 词典） -->
        <div class="panel">
          <h2>导入单词（接入 AI 词典）</h2>
          <p class="small">
            粘贴想学的单词，每行一个，系统会自动用 AI 词典补充音标、释义和例句。
          </p>
          <textarea
            v-model="importText"
            class="import-area"
            placeholder="每行一个单词，例如：&#10;apple&#10;banana&#10;orange"
          ></textarea>
          <button class="btn-sm" @click="importWords">导入到我的单词库</button>
        </div>
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { userKey } from "../services/userStorage";

// Playground 对应的 localStorage key
const LS_AUDIO = "wordTrainerAudioConfig";
const LS_BUTTONS = "wordTrainerButtonLabels";
const LS_THEME = "wordTrainerTheme";
const LS_STORY = "wordTrainerStoryConfig";
const LS_CARD = "wordTrainerCardConfig";

// API 基础
const API_BASE = "http://localhost:3100/api/words";

// 导师信息（从 localStorage 读取）
const agentProfile = ref(null);
const mentorAvatarEmojiInitial = computed(() => {
  if (!agentProfile.value) return "📘";
  return agentProfile.value.mentorAvatarEmoji || "📘";
});
const mentorSlogan = computed(() => agentProfile.value?.mentorSlogan || "");
const medalName = computed(() => agentProfile.value?.medalName || "");
const themeClass = computed(() => {
  const theme = agentProfile.value?.theme || "blue";
  if (theme === "green") return "theme-green";
  if (theme === "orange") return "theme-orange";
  return "theme-blue";
});

// 单词机配置 & 数据
const config = ref(null);
const todayWords = ref([]);
const currentIndex = ref(0);
const allWords = ref([]);
const importText = ref("");

// 学习助手开关（故事 / 测验 / 小结）
const prefs = ref({
  showStory: true,
  showQuiz: true,
  showSummary: true,
});
function loadPrefs() {
  try {
    const raw = localStorage.getItem("wordTrainerPrefs");
    if (raw) {
      const saved = JSON.parse(raw);
      prefs.value = { ...prefs.value, ...saved };
    }
  } catch (e) {
    // ignore
  }
}
function savePrefs() {
  try {
    localStorage.setItem("wordTrainerPrefs", JSON.stringify(prefs.value));
  } catch (e) {
    // ignore
  }
}

// 从 Playground 接 audio / button / theme / card 配置
const audioConfig = ref({
  rate: 0.75,
  pitch: 1.0,
  volume: 1.0,
});
const buttonLabels = ref({
  hardLabel: "不太会 / 我要再背",
  masteredLabel: "认识，下一个",
});
const themeConfig = ref({
  primaryColor: "#3182ce",
  accentColor: "#38a169",
});
const cardConfig = ref({
  showPhonetic: true,
  withExample: true,
  allowNote: true,
});

// 故事默认配置
const storyStyle = ref("funny");
const storyLength = ref("short");
const storyTone = ref("teacher");

// 用 CSS 变量给整页染色
const rootStyle = computed(() => ({
  "--primary-color": themeConfig.value.primaryColor || "#3182ce",
  "--accent-color": themeConfig.value.accentColor || "#38a169",
}));

// 🔊 发音相关状态
const speechRate = ref(0.75); // 默认稍微慢一点
function safeSpeak(text) {
  if (typeof window === "undefined" || !("speechSynthesis" in window)) {
    alert("当前浏览器不支持语音播放，可以换成 Chrome 再试试。");
    return;
  }
  if (!text) return;
  const utter = new window.SpeechSynthesisUtterance(text);
  utter.lang = "en-US";
  const rateFromConfig = audioConfig.value.rate ?? 1;
  const finalRate = speechRate.value || rateFromConfig || 1;
  utter.rate = Math.max(0.1, Math.min(finalRate, 2));
  utter.pitch = audioConfig.value.pitch ?? 1;
  utter.volume = audioConfig.value.volume ?? 1;
  window.speechSynthesis.cancel();
  window.speechSynthesis.speak(utter);
}
function playWordSound() {
  if (!currentWord.value) return;
  safeSpeak(currentWord.value.text);
}
function playExampleSentence() {
  if (!currentWord.value || !currentWord.value.examples?.length) return;
  safeSpeak(currentWord.value.examples[0]);
}

// 文案：模板 / 强度 / 复习流派 / 语音模式
const templateName = computed(() => {
  if (!config.value) return "";
  const t = config.value.template;
  if (t === "exam") return "考试提分型单词机";
  if (t === "fun") return "轻松兴趣型单词机";
  if (t === "speaking") return "口语发音型单词机";
  return "自定义单词机";
});
const intensityLabel = computed(() => {
  if (!config.value) return "";
  const t = config.value.intensity;
  if (t === "easy") return "轻松模式";
  if (t === "normal") return "标准模式";
  if (t === "hard") return "冲刺模式";
  return "";
});
const reviewLabel = computed(() => {
  if (!config.value) return "";
  const t = config.value.reviewStyle;
  if (t === "lazy") return "佛系背单词";
  if (t === "standard") return "稳定进步";
  if (t === "exam") return "考试冲刺";
  return "";
});
const voiceLabel = computed(() => {
  if (!config.value) return "";
  const t = config.value.voiceMode;
  if (t === "relaxed") return "宽松";
  if (t === "normal") return "标准";
  if (t === "strict") return "严格";
  return "关闭";
});

// 当前单词
const currentWord = computed(() => {
  if (!todayWords.value.length) return null;
  if (currentIndex.value >= todayWords.value.length) return null;
  return todayWords.value[currentIndex.value];
});
const totalToday = computed(() => todayWords.value.length);

// 掌握 / 难词数量
const masteredCount = computed(
  () => allWords.value.filter((w) => (w.mastery || 0) >= 3).length
);
const hardWords = computed(() =>
  allWords.value.filter((w) => (w.mastery || 0) < 2)
);
const hardCount = computed(() => hardWords.value.length);

// AI 功能：状态变量
const wordFamilyText = ref("");
const storyText = ref("");
const quizText = ref("");
const summaryText = ref("");
const loadingFamily = ref(false);
const loadingStory = ref(false);
const loadingQuiz = ref(false);
const loadingSummary = ref(false);

// 初始化
onMounted(() => {
  loadPrefs();

  // ① 导师信息
  try {
    const mentorKey = userKey("wordTrainerMentorConfig");
    const rawMentor = localStorage.getItem(mentorKey);
    if (rawMentor) {
      const m = JSON.parse(rawMentor);
      agentProfile.value = {
        mentorName:
          m.mentorName ||
          (m.studentName ? m.studentName + "的单词教练" : "我的单词教练"),
        mentorAvatarEmoji: m.mentorAvatarEmoji || "📘",
        theme: m.theme || "blue",
        mentorSlogan: m.mentorSlogan || "",
        medalName: m.medalName || "单词小猎人",
      };
      localStorage.setItem("agent-profile", JSON.stringify(agentProfile.value));
    } else {
      const savedProfile = localStorage.getItem("agent-profile");
      if (savedProfile) {
        agentProfile.value = JSON.parse(savedProfile);
      } else {
        const cfgKey = userKey("wordMachineConfig");
        const cfgRaw =
          localStorage.getItem(cfgKey) ||
          localStorage.getItem("wordMachineConfig");
        if (cfgRaw) {
          const cfg = JSON.parse(cfgRaw);
          const profile = {
            mentorName:
              cfg.mentorName ||
              (cfg.studentName
                ? cfg.studentName + "的单词教练"
                : "我的单词教练"),
            mentorAvatarEmoji: cfg.avatarEmoji || cfg.avatar || "📘",
            theme: cfg.theme || "blue",
            mentorSlogan: cfg.slogan || "",
            medalName: cfg.medalName || "单词小猎人",
          };
          agentProfile.value = profile;
          localStorage.setItem("agent-profile", JSON.stringify(profile));
        }
      }
    }
  } catch (e) {
    console.error("读取导师信息失败", e);
  }

  // ② 读取配置
  try {
    const cfgKey = userKey("wordMachineConfig");
    const raw =
      localStorage.getItem(cfgKey) ||
      localStorage.getItem("wordMachineConfig");
    if (raw) {
      config.value = JSON.parse(raw);
    }
    const planKey = userKey("wordTrainerPlanConfig");
    const planRaw = localStorage.getItem(planKey);
    if (planRaw && config.value) {
      const plan = JSON.parse(planRaw);
      if (plan.intensity) config.value.intensity = plan.intensity;
      if (plan.reviewStyle) config.value.reviewStyle = plan.reviewStyle;
    }
  } catch (e) {
    console.error("读取配置失败", e);
  }
  if (!config.value) return;

  // ③ 读取单词库
  try {
    const wordsKey = userKey("wordMachineWords");
    const rawWords =
      localStorage.getItem(wordsKey) ||
      localStorage.getItem("wordMachineWords");
    if (rawWords) {
      allWords.value = JSON.parse(rawWords);
    }
  } catch (e) {
    console.error("读取单词库失败", e);
  }

  // 没有单词时的示例
  if (!allWords.value.length) {
    const now = Date.now();
    allWords.value = [
      {
        id: "apple",
        text: "apple",
        phonetic: "ˈæpəl",
        meaning: "苹果",
        examples: ["The girl picked an apple from the tree."],
        mastery: 0,
        nextReviewAt: now,
      },
      {
        id: "banana",
        text: "banana",
        phonetic: "bəˈnɑːnə",
        meaning: "香蕉",
        examples: ["Monkeys like eating bananas."],
        mastery: 0,
        nextReviewAt: now,
      },
      {
        id: "orange",
        text: "orange",
        phonetic: "ˈɒrɪndʒ",
        meaning: "橙子；橙色",
        examples: ["She drank a glass of orange juice."],
        mastery: 0,
        nextReviewAt: now,
      },
    ];
    saveWords();
  }

  prepareTodayQueue();

  // ④ 从 Playground 读取 audio / buttons / theme / story / card
  try {
    const audioKey = userKey(LS_AUDIO);
    const rawAudio =
      localStorage.getItem(audioKey) || localStorage.getItem(LS_AUDIO);
    if (rawAudio) {
      const obj = JSON.parse(rawAudio);
      audioConfig.value = { ...audioConfig.value, ...obj };
      if (audioConfig.value.rate != null) {
        speechRate.value = audioConfig.value.rate;
      }
    }
  } catch (e) {
    console.warn("读取 audioConfig 失败", e);
  }
  try {
    const btnKey = userKey(LS_BUTTONS);
    const rawButtons =
      localStorage.getItem(btnKey) || localStorage.getItem(LS_BUTTONS);
    if (rawButtons) {
      const obj = JSON.parse(rawButtons);
      buttonLabels.value = { ...buttonLabels.value, ...obj };
    }
  } catch (e) {
    console.warn("读取 buttonLabels 失败", e);
  }
  try {
    const themeKey = userKey(LS_THEME);
    const rawTheme =
      localStorage.getItem(themeKey) || localStorage.getItem(LS_THEME);
    if (rawTheme) {
      const obj = JSON.parse(rawTheme);
      themeConfig.value = { ...themeConfig.value, ...obj };
    }
  } catch (e) {
    console.warn("读取 themeConfig 失败", e);
  }
  try {
    const storyKey = userKey(LS_STORY);
    const rawStory =
      localStorage.getItem(storyKey) || localStorage.getItem(LS_STORY);
    if (rawStory) {
      const obj = JSON.parse(rawStory);
      if (obj.style) storyStyle.value = obj.style;
      if (obj.length) storyLength.value = obj.length;
      if (obj.tone) storyTone.value = obj.tone;
    }
  } catch (e) {
    console.warn("读取 storyConfig 失败", e);
  }
  try {
    const cardKey = userKey(LS_CARD);
    const rawCard =
      localStorage.getItem(cardKey) || localStorage.getItem(LS_CARD);
    if (rawCard) {
      const obj = JSON.parse(rawCard);
      cardConfig.value = { ...cardConfig.value, ...obj };
    }
  } catch (e) {
    console.warn("读取 cardConfig 失败", e);
  }
});

// 保存单词库
function saveWords() {
  try {
    const wordsKey = userKey("wordMachineWords");
    localStorage.setItem(wordsKey, JSON.stringify(allWords.value));
  } catch (e) {
    console.error("保存单词库失败", e);
  }
}

// 今日任务队列
function getIntensityCounts() {
  if (!config.value) return { newWords: 10, reviewWords: 10 };
  const base = config.value.intensity;
  if (base === "easy") return { newWords: 5, reviewWords: 10 };
  if (base === "hard") return { newWords: 15, reviewWords: 15 };
  return { newWords: 10, reviewWords: 10 };
}
function prepareTodayQueue() {
  const now = Date.now();
  const { newWords, reviewWords } = getIntensityCounts();
  const newList = [];
  const reviewList = [];

  for (const w of allWords.value) {
    if (!w.nextReviewAt || w.nextReviewAt <= now) {
      if ((w.mastery || 0) === 0) {
        if (newList.length < newWords) newList.push(w);
      } else {
        if (reviewList.length < reviewWords) reviewList.push(w);
      }
    }
  }

  const rest = allWords.value.filter(
    (w) => !newList.includes(w) && !reviewList.includes(w)
  );
  while (newList.length < newWords && rest.length) {
    newList.push(rest.shift());
  }
  while (reviewList.length < reviewWords && rest.length) {
    reviewList.push(rest.shift());
  }

  todayWords.value = [...newList, ...reviewList];
  currentIndex.value = 0;
}

// 计算下次复习时间
function calcNextReview(mastery) {
  if (!config.value) return Date.now() + 24 * 3600 * 1000;
  const style = config.value.reviewStyle;
  let mins = 60;
  if (style === "lazy") {
    if (mastery <= 1) mins = 60;
    else if (mastery === 2) mins = 24 * 60;
    else if (mastery === 3) mins = 3 * 24 * 60;
    else mins = 7 * 24 * 60;
  } else if (style === "standard") {
    if (mastery <= 1) mins = 20;
    else if (mastery === 2) mins = 60;
    else if (mastery === 3) mins = 24 * 60;
    else mins = 3 * 24 * 60;
  } else if (style === "exam") {
    if (mastery <= 1) mins = 10;
    else if (mastery === 2) mins = 30;
    else if (mastery === 3) mins = 12 * 60;
    else mins = 24 * 60;
  }
  return Date.now() + mins * 60 * 1000;
}

// 标记“认识”
function markMastered() {
  if (!currentWord.value) return;
  const w = currentWord.value;
  w.mastery = (w.mastery || 0) + 1;
  w.nextReviewAt = calcNextReview(w.mastery);
  saveWords();
  currentIndex.value += 1;
}

// 标记“不会”
function markHard() {
  if (!currentWord.value) return;
  const w = currentWord.value;
  w.mastery = Math.max(0, (w.mastery || 0) - 1);
  w.nextReviewAt = Date.now() + 10 * 60 * 1000;
  saveWords();
  currentIndex.value += 1;
}

// ✅ 删除当前单词（从 todayWords + allWords 里一起删）
function deleteCurrentWord() {
  if (!currentWord.value) return;
  const w = currentWord.value;

  if (!confirm(`确定要把单词 "${w.text}" 从单词库里删除吗？以后训练将不会再出现。`)) {
    return;
  }

  // 1. 从今日队列删除
  todayWords.value = todayWords.value.filter((item) => item.id !== w.id);

  // 2. 从总单词库删除
  allWords.value = allWords.value.filter((item) => item.id !== w.id);

  // 3. 保存
  saveWords();

  // 4. 修正 currentIndex（避免越界）
  if (currentIndex.value >= todayWords.value.length) {
    currentIndex.value = todayWords.value.length - 1;
  }
}

// 再来一轮今天的单词
function restartToday() {
  currentIndex.value = 0;
}

// 错词挑战：跳到指定单词
function jumpToWord(id) {
  const idx = todayWords.value.findIndex((w) => w.id === id);
  if (idx >= 0) {
    currentIndex.value = idx;
  } else {
    const target = allWords.value.find((w) => w.id === id);
    if (target) {
      todayWords.value.push(target);
      currentIndex.value = todayWords.value.length - 1;
    }
  }
}

// 导入单词 + AI 词典
async function importWords() {
  const text = importText.value.trim();
  if (!text) return;

  const lines = text
    .split(/\r?\n/)
    .map((l) => l.trim())
    .filter(Boolean);

  const existingIds = new Set(allWords.value.map((w) => w.id.toLowerCase()));
  const toLookup = [];

  for (const line of lines) {
    const word = line.toLowerCase();
    if (!word) continue;
    if (existingIds.has(word)) continue;
    existingIds.add(word);
    toLookup.push(word);
  }

  if (!toLookup.length) {
    alert("这些单词已经在单词库里啦～");
    return;
  }

  const now = Date.now();
  let items = [];

  try {
    const resp = await fetch(`${API_BASE}/lookup`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ words: toLookup }),
    });
    const data = await resp.json();
    if (!resp.ok || !data.ok) {
      throw new Error(data.error || "AI 词典接口错误");
    }
    items = Array.isArray(data.items) ? data.items : [];
  } catch (e) {
    console.error("调用 AI 词典失败，使用兜底逻辑。", e);
    items = toLookup.map((w) => ({
      word: w,
      phonetic: "",
      meaning: "（AI 词典暂时不可用，只先保存单词本身）",
      examples: [],
    }));
  }

  const map = new Map();
  for (const item of items) {
    const key = (item.word || "").toLowerCase();
    if (!key) continue;
    map.set(key, item);
  }

  for (const w of toLookup) {
    const info = map.get(w) || {};
    allWords.value.push({
      id: w,
      text: w,
      phonetic: info.phonetic || "",
      meaning: info.meaning || "（暂时没有释义）",
      examples:
        info.examples ||
        (info.example ? [String(info.example)] : []),
      mastery: 0,
      nextReviewAt: now,
    });
  }

  saveWords();
  importText.value = "";
  alert("已通过 AI 词典导入到单词库，之后任务中会安排这些新单词。");
}

// AI 调用封装（用于 family/story/quiz/summary）
async function callWordsApi(endpoint, payload) {
  const resp = await fetch(`${API_BASE}/${endpoint}`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(payload),
  });
  const data = await resp.json();
  if (!resp.ok || !data.ok) {
    throw new Error(data.error || "Qwen API error");
  }
  return data.text;
}

// 1）举一反三：当前单词的词族 / 近义词 / 反义词
async function loadWordFamily() {
  if (!currentWord.value) return;
  try {
    loadingFamily.value = true;
    wordFamilyText.value = "（正在向 AI 请求解析，请稍等…）";
    const text = await callWordsApi("family", {
      word: currentWord.value.text,
      examples: currentWord.value.examples || [],
    });
    wordFamilyText.value = text;
  } catch (e) {
    console.error(e);
    wordFamilyText.value = "（千问接口出错了，可以稍后再试。）";
  } finally {
    loadingFamily.value = false;
  }
}

// 2）错词故事工厂
async function makeStoryFromHardWords() {
  const words = hardWords.value.map((w) => w.text);
  if (!words.length) {
    storyText.value = "目前没有错词可以写故事，先多做几道题吧～";
    return;
  }
  try {
    loadingStory.value = true;
    storyText.value = "（正在请 AI 编故事，请稍等…）";
    const text = await callWordsApi("story", {
      words,
      style: storyStyle.value,
      length: storyLength.value, // 和后端 /story 的 length 对齐
      tone: storyTone.value,
      studentName: config.value?.studentName || "",
    });
    storyText.value = text;
  } catch (e) {
    console.error(e);
    storyText.value = "（千问接口出错了，没生成成功。）";
  } finally {
    loadingStory.value = false;
  }
}

// 3）错词小测验
async function makeQuizFromHardWords() {
  const words = hardWords.value.map((w) => w.text);
  if (!words.length) {
    quizText.value = "目前没有错词可以出题，先多做几道题吧～";
    return;
  }
  try {
    loadingQuiz.value = true;
    quizText.value = "（AI 正在出题，请稍等…）";
    const text = await callWordsApi("quiz", { words });
    quizText.value = text;
  } catch (e) {
    console.error(e);
    quizText.value = "（千问接口出错了，没出题成功。）";
  } finally {
    loadingQuiz.value = false;
  }
}

// 4）学习小结 + 鼓励语
async function makeStudySummary() {
  try {
    loadingSummary.value = true;
    summaryText.value = "（AI 正在写给你的学习反馈…）";
    const text = await callWordsApi("summary", {
      learnedCount: masteredCount.value,
      hardCount: hardCount.value,
      streak: 1,
      wordsToday: todayWords.value.map((w) => w.text),
      studentName: config.value?.studentName || "",
    });
    summaryText.value = text;
  } catch (e) {
    console.error(e);
    summaryText.value = "（千问接口出错了，暂时没拿到总结。）";
  } finally {
    loadingSummary.value = false;
  }
}
</script>

<style scoped>
.trainer-back {
  margin-bottom: 12px;
}
.trainer-page {
  max-width: 1120px;
  margin: 1.5em auto;
  padding: 1.5em 1.2em 1.5em;
  background: #ffffff;
  border-radius: 1.2em;
  box-shadow: var(--shadow-lg);
}
.topbar h1 {
  font-size: 1.6rem;
  font-weight: 700;
  margin-bottom: 0.3rem;
  color: #1a202c;
}
.sub {
  font-size: 0.9rem;
  color: #4a5568;
  margin-bottom: 0.3rem;
}
.sub.warn {
  color: #c05621;
}

/* 导师卡片 */
.mentor-box {
  display: flex;
  align-items: center;
  padding: 0.6rem 0.8rem;
  border-radius: 0.9rem;
  margin-bottom: 0.5rem;
  background: #edf2ff;
}
.theme-blue {
  background: #ebf4ff;
}
.theme-green {
  background: #e6fffa;
}
.theme-orange {
  background: #fffaf0;
}
.mentor-avatar {
  width: 40px;
  height: 40px;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 0.7rem;
}
.mentor-avatar-emoji {
  font-size: 22px;
}
.mentor-meta {
  flex: 1;
  min-width: 0;
}
.mentor-name {
  font-size: 1rem;
  font-weight: 600;
  color: #1a202c;
}
.mentor-desc {
  font-size: 0.85rem;
  color: #4a5568;
  margin-top: 0.1rem;
}
.mentor-slogan {
  font-size: 0.82rem;
  color: #2b6cb0;
  margin-top: 0.1rem;
}

.empty {
  text-align: center;
  padding: 1.5rem 0.5rem 0.5rem;
}
.btn {
  display: inline-block;
  border-radius: 999px;
  padding: 0.5rem 1.4rem;
  background: var(--primary-color, #3182ce);
  color: #fff;
  border: none;
  cursor: pointer;
  font-size: 0.95rem;
}
.btn:hover {
  background: #2b6cb0;
}

.content {
  display: grid;
  grid-template-columns: minmax(0, 3fr) minmax(0, 2.2fr);
  gap: 1rem;
  margin-top: 0.6rem;
}
.left,
.right {
  display: flex;
  flex-direction: column;
  gap: 0.7rem;
}

.progress-card {
  border-radius: 0.9rem;
  background: #ebf8ff;
  padding: 0.7rem 0.8rem;
  font-size: 0.9rem;
  color: #2a4365;
}
.progress-num {
  font-size: 1.4rem;
  font-weight: 700;
  margin: 0.1rem 0;
}
.small {
  font-size: 0.84rem;
  color: #4a5568;
}

.word-card {
  border-radius: 0.9rem;
  border: 1px solid #e2e8f0;
  padding: 0.8rem 0.9rem;
  background: #f9fafb;
}
.word-main {
  margin-bottom: 0.5rem;
}
.word-text {
  font-size: 1.5rem;
  font-weight: 700;
  color: #1a202c;
}
.word-phonetic {
  font-size: 0.95rem;
  color: #4a5568;
  margin-top: 0.1rem;
}
.word-meaning {
  margin-top: 0.25rem;
  font-size: 0.96rem;
  color: #2d3748;
}

/* 🔊 发音行 */
.pronounce-row {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 0.4rem;
  margin-top: 0.45rem;
}
.rate-label select {
  margin-left: 0.25rem;
  font-size: 0.82rem;
}

/* 例句 & 笔记 */
.examples {
  margin-top: 0.5rem;
}
.ex-title {
  font-size: 0.9rem;
  font-weight: 600;
  margin-bottom: 0.2rem;
}
.examples ul {
  padding-left: 1em;
  margin: 0;
}
.examples li {
  font-size: 0.9rem;
  color: #4a5568;
}
.note-block {
  margin-top: 0.5rem;
}
.note-label {
  font-size: 0.88rem;
  color: #4a5568;
  display: block;
  margin-bottom: 0.2rem;
}
.note-text {
  width: 100%;
  min-height: 60px;
  border-radius: 0.6rem;
  border: 1px solid #cbd5e0;
  padding: 0.4rem 0.5rem;
  font-size: 0.9rem;
  resize: vertical;
}

/* 当前单词 AI 辅助区 */
.ai-panel {
  margin-top: 0.6rem;
  padding-top: 0.5rem;
  border-top: 1px dashed #e2e8f0;
}
.ai-result {
  margin-top: 0.4rem;
  padding: 0.5rem 0.6rem;
  background: #f7fafc;
  border-radius: 0.6rem;
  max-height: 220px;
  overflow-y: auto;
  font-size: 0.86rem;
  line-height: 1.4;
}
.ai-hint {
  margin-top: 0.3rem;
  font-size: 0.82rem;
  color: #718096;
}

/* 按钮区 */
.actions {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-end;
  gap: 0.5rem;
  margin-top: 0.6rem;
}
.btn-primary,
.btn-secondary,
.btn-warning {
  border-radius: 999px;
  padding: 0.4rem 1.1rem;
  border: none;
  cursor: pointer;
  font-size: 0.9rem;
}
.btn-primary {
  background: var(--accent-color, #38a169);
  color: #fff;
}
.btn-primary:hover {
  background: #2f855a;
}
.btn-secondary {
  background: #edf2f7;
  color: #2d3748;
}
.btn-secondary:hover {
  background: #e2e8f0;
}
/* 新增：删除按钮 */
.btn-warning {
  background: #f56565;
  color: #fff;
}
.btn-warning:hover {
  background: #c53030;
}

.finished-card {
  border-radius: 0.9rem;
  border: 1px solid #e2e8f0;
  padding: 0.9rem 0.9rem;
  text-align: left;
  background: #f9fafb;
}
.finished-card h2 {
  margin-top: 0;
  margin-bottom: 0.3rem;
}

.panel {
  border-radius: 0.9rem;
  border: 1px solid #e2e8f0;
  padding: 0.7rem 0.8rem;
  background: #ffffff;
}
.panel h2 {
  font-size: 1rem;
  margin-bottom: 0.2rem;
}

.btn-sm {
  display: inline-block;
  margin-top: 0.4rem;
  border-radius: 999px;
  padding: 0.3rem 0.9rem;
  border: none;
  background: var(--primary-color, #3182ce);
  color: #fff;
  font-size: 0.85rem;
  cursor: pointer;
}
.btn-sm:hover {
  background: #2b6cb0;
}
.btn-sm.ghost {
  background: #edf2f7;
  color: #2d3748;
}
.btn-sm.ghost:hover {
  background: #e2e8f0;
}

.empty-box {
  font-size: 0.9rem;
  color: #718096;
  padding: 0.4rem 0;
}
.hard-list {
  list-style: none;
  margin: 0.3rem 0 0;
  padding: 0;
}
.hard-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.25rem 0;
  font-size: 0.9rem;
  cursor: pointer;
}
.hard-list li:hover {
  color: #2b6cb0;
}
.w-text {
  font-weight: 500;
}
.w-meta {
  font-size: 0.8rem;
  color: #718096;
}

.import-area {
  width: 100%;
  min-height: 60px;
  border-radius: 0.6rem;
  border: 1px solid #cbd5e0;
  padding: 0.4rem 0.5rem;
  font-size: 0.86rem;
  resize: vertical;
  margin-top: 0.3rem;
}

.story-controls {
  display: flex;
  gap: 0.8rem;
  margin: 0.3rem 0 0.1rem;
  flex-wrap: wrap;
}
.story-controls select {
  margin-left: 0.3rem;
}

/* 学习助手开关 */
.toggle {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  font-size: 0.86rem;
  margin-top: 0.2rem;
}
</style>
