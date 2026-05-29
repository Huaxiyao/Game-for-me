# 打字速度测试 · 重写实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 重写 `typing-test.html`，实现极简专注风格的打字速度测试，支持中英文、三种模式、难度选择、历史记录、排行榜等功能。

**Architecture:** 单文件 HTML 架构，CSS 内联样式 + JavaScript 内联逻辑。核心打字体验优先，高级功能（历史、排行榜、自定义文本）作为可折叠面板渐进展示。

**Tech Stack:** 纯 HTML/CSS/JavaScript，无外部依赖，localStorage 存储历史数据。

---

## 文件结构

单文件 `typing-test.html`，包含：
- HTML 结构
- CSS 样式（内联 `<style>`）
- JavaScript 逻辑（内联 `<script>`）

---

### Task 1: HTML 结构 + 基础样式

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 创建基础 HTML 结构**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>打字速度测试</title>
<style>
/* 基础样式将在下一步添加 */
</style>
</head>
<body>
<div class="container">
  <!-- 顶部控制栏 -->
  <div class="controls">
    <div class="lang-switch">
      <button class="lang-btn active" data-lang="zh">中文</button>
      <button class="lang-btn" data-lang="en">English</button>
    </div>
    <div class="mode-switch">
      <button class="mode-btn active" data-mode="text">按文本打字</button>
      <button class="mode-btn" data-mode="countdown">60秒计时</button>
      <button class="mode-btn" data-mode="random">随机单词</button>
    </div>
    <div class="difficulty-switch">
      <button class="diff-btn" data-diff="easy">简单</button>
      <button class="diff-btn active" data-diff="medium">中等</button>
      <button class="diff-btn" data-diff="hard">困难</button>
    </div>
  </div>

  <!-- 核心打字区域 -->
  <div class="typing-area">
    <div class="text-display" id="textDisplay"></div>
    <textarea class="text-input" id="textInput" placeholder="在此输入..." disabled></textarea>
  </div>

  <!-- 统计栏 -->
  <div class="stats">
    <div class="stat-item">
      <div class="stat-label">速度</div>
      <div class="stat-value" id="wpm">0</div>
      <div class="stat-unit" id="wpmUnit">字/分</div>
    </div>
    <div class="stat-item">
      <div class="stat-label">准确率</div>
      <div class="stat-value" id="accuracy">--</div>
      <div class="stat-unit">正确</div>
    </div>
    <div class="stat-item">
      <div class="stat-label">时间</div>
      <div class="stat-value" id="timer">0s</div>
      <div class="stat-unit">已用</div>
    </div>
  </div>

  <!-- 快捷键提示 -->
  <div class="shortcuts">
    按 Enter 重新开始 · Tab 展开高级选项
  </div>

  <!-- 高级选项面板 -->
  <div class="advanced-panel" id="advancedPanel">
    <div class="panel-tabs">
      <button class="panel-tab active" data-tab="history">历史记录</button>
      <button class="panel-tab" data-tab="leaderboard">排行榜</button>
      <button class="panel-tab" data-tab="custom">自定义文本</button>
    </div>
    <div class="panel-content">
      <div class="tab-content active" id="historyTab">
        <div class="history-list" id="historyList"></div>
        <button class="clear-btn" id="clearHistory">清除历史</button>
      </div>
      <div class="tab-content" id="leaderboardTab">
        <div class="leaderboard-list" id="leaderboardList"></div>
      </div>
      <div class="tab-content" id="customTab">
        <textarea class="custom-input" id="customText" placeholder="输入自定义文本..."></textarea>
        <button class="apply-btn" id="applyCustom">应用</button>
      </div>
    </div>
  </div>
</div>

<script>
// JavaScript 逻辑将在后续任务中添加
</script>
</body>
</html>
```

- [ ] **Step 2: 验证 HTML 结构**

在浏览器中打开 `typing-test.html`，确认：
- 页面能正常加载
- 所有按钮和输入框可见
- 布局基本正确

---

### Task 2: 基础 CSS 样式

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 添加极简风格 CSS**

```css
* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  min-height: 100vh;
  background: #f8fafc;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #1e293b;
}

.container {
  max-width: 700px;
  width: 100%;
  padding: 32px 24px;
}

/* 顶部控制栏 */
.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
  flex-wrap: wrap;
  gap: 12px;
}

.lang-switch, .mode-switch, .difficulty-switch {
  display: flex;
  gap: 8px;
}

.lang-btn, .mode-btn, .diff-btn {
  padding: 6px 14px;
  border: 1.5px solid #e2e8f0;
  border-radius: 8px;
  font-size: 12px;
  font-weight: 500;
  background: #fff;
  color: #64748b;
  cursor: pointer;
  transition: all 0.2s;
}

.lang-btn.active, .mode-btn.active, .diff-btn.active {
  border-color: #818cf8;
  color: #6366f1;
  background: #eef2ff;
}

.lang-btn:hover, .mode-btn:hover, .diff-btn:hover {
  border-color: #c7d2fe;
}

/* 核心打字区域 */
.typing-area {
  background: #fff;
  border-radius: 12px;
  padding: 24px;
  margin-bottom: 20px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.04);
  border: 1px solid #e2e8f0;
}

.text-display {
  font-size: 20px;
  line-height: 1.8;
  color: #1e293b;
  letter-spacing: 0.5px;
  margin-bottom: 16px;
  min-height: 60px;
}

.text-display .correct { color: #10b981; }
.text-display .incorrect { color: #ef4444; text-decoration: underline; }
.text-display .current { background: #eef2ff; border-radius: 3px; }
.text-display .untyped { color: #94a3b8; }

.text-input {
  width: 100%;
  min-height: 60px;
  border: 2px solid #e2e8f0;
  border-radius: 10px;
  padding: 12px 16px;
  font-size: 16px;
  font-family: inherit;
  resize: none;
  outline: none;
  background: #fff;
  color: #1e293b;
  transition: border-color 0.2s;
}

.text-input:focus { border-color: #818cf8; }
.text-input:disabled { background: #f8fafc; color: #94a3b8; }

/* 统计栏 */
.stats {
  display: flex;
  justify-content: center;
  gap: 32px;
  margin-bottom: 16px;
}

.stat-item { text-align: center; }

.stat-label {
  font-size: 10px;
  font-weight: 600;
  color: #94a3b8;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 4px;
}

.stat-value {
  font-size: 28px;
  font-weight: 700;
  color: #1e293b;
  font-variant-numeric: tabular-nums;
}

.stat-unit {
  font-size: 10px;
  color: #94a3b8;
}

/* 快捷键提示 */
.shortcuts {
  text-align: center;
  font-size: 12px;
  color: #94a3b8;
  margin-bottom: 20px;
}

/* 高级选项面板 */
.advanced-panel {
  background: #fff;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.04);
  border: 1px solid #e2e8f0;
  display: none;
}

.advanced-panel.visible { display: block; }

.panel-tabs {
  display: flex;
  gap: 12px;
  margin-bottom: 16px;
  border-bottom: 1px solid #e2e8f0;
  padding-bottom: 12px;
}

.panel-tab {
  padding: 6px 12px;
  border: none;
  background: none;
  font-size: 13px;
  font-weight: 500;
  color: #64748b;
  cursor: pointer;
  border-radius: 6px;
}

.panel-tab.active {
  color: #6366f1;
  background: #eef2ff;
}

.tab-content { display: none; }
.tab-content.active { display: block; }

.history-list, .leaderboard-list {
  max-height: 300px;
  overflow-y: auto;
}

.history-item, .leaderboard-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px solid #f1f5f9;
  font-size: 13px;
}

.history-item:last-child, .leaderboard-item:last-child {
  border-bottom: none;
}

.clear-btn, .apply-btn {
  margin-top: 12px;
  padding: 8px 16px;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 12px;
  font-weight: 500;
  background: #fff;
  color: #64748b;
  cursor: pointer;
}

.clear-btn:hover, .apply-btn:hover {
  background: #f8fafc;
}

.custom-input {
  width: 100%;
  min-height: 100px;
  border: 1.5px solid #e2e8f0;
  border-radius: 8px;
  padding: 10px 12px;
  font-size: 14px;
  font-family: inherit;
  resize: vertical;
  outline: none;
}

.custom-input:focus { border-color: #818cf8; }

/* 响应式 */
@media (max-width: 600px) {
  .container { padding: 20px 16px; }
  .controls { flex-direction: column; align-items: stretch; }
  .lang-switch, .mode-switch, .difficulty-switch { justify-content: center; }
  .text-display { font-size: 17px; }
  .stat-value { font-size: 22px; }
  .stats { gap: 20px; }
}

/* 深色模式 */
@media (prefers-color-scheme: dark) {
  body { background: #1e293b; color: #e2e8f0; }
  .typing-area, .advanced-panel {
    background: #0f172a;
    border-color: #334155;
  }
  .text-input {
    background: #0f172a;
    border-color: #334155;
    color: #e2e8f0;
  }
  .text-input:focus { border-color: #818cf8; }
  .text-input:disabled { background: #1e293b; color: #64748b; }
  .text-display .untyped { color: #64748b; }
  .lang-btn, .mode-btn, .diff-btn {
    background: #1e293b;
    border-color: #334155;
    color: #94a3b8;
  }
  .lang-btn.active, .mode-btn.active, .diff-btn.active {
    background: #1e1b4b;
    border-color: #818cf8;
    color: #a5b4fc;
  }
  .stat-value { color: #e2e8f0; }
  .advanced-panel { background: #0f172a; border-color: #334155; }
  .panel-tab { color: #94a3b8; }
  .panel-tab.active { background: #1e1b4b; color: #a5b4fc; }
  .history-item, .leaderboard-item { border-color: #1e293b; }
  .clear-btn, .apply-btn {
    background: #1e293b;
    border-color: #334155;
    color: #94a3b8;
  }
  .custom-input {
    background: #0f172a;
    border-color: #334155;
    color: #e2e8f0;
  }
}
```

- [ ] **Step 2: 验证样式**

在浏览器中打开 `typing-test.html`，确认：
- 极简风格正确显示
- 按钮样式正确
- 深色模式下样式正确（切换系统深色模式测试）

---

### Task 3: 核心打字逻辑

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 添加基础 JavaScript 逻辑**

```javascript
// 状态变量
let lang = 'zh';
let mode = 'text';
let difficulty = 'medium';
let target = '';
let started = false;
let completed = false;
let startTime = 0;
let timer = null;
let panelVisible = false;

// DOM 元素
const textDisplay = document.getElementById('textDisplay');
const textInput = document.getElementById('textInput');
const wpmEl = document.getElementById('wpm');
const accuracyEl = document.getElementById('accuracy');
const timerEl = document.getElementById('timer');
const wpmUnitEl = document.getElementById('wpmUnit');
const advancedPanel = document.getElementById('advancedPanel');

// 文本数据
const texts = {
  zh: {
    easy: [
      '春天的早晨阳光明媚小鸟在枝头欢快地歌唱',
      '生活不止眼前的苟且还有诗和远方的田野',
      '时间是一只藏在黑暗中的温柔的手',
      '世界上最宽阔的是海洋比海洋更宽阔的是天空'
    ],
    medium: [
      '春天的早晨，阳光明媚，小鸟在枝头欢快地歌唱。微风吹过湖面，泛起层层涟漪。',
      '生活不止眼前的苟且，还有诗和远方的田野。你赤手空拳来到人世间，为找到那片海不顾一切。',
      '时间是一只藏在黑暗中的温柔的手，在你一出神、一恍惚之间，物走星移。',
      '世界上最宽阔的是海洋，比海洋更宽阔的是天空，比天空更宽阔的是人的胸怀。'
    ],
    hard: [
      '春天的早晨，阳光明媚，小鸟在枝头欢快地歌唱。微风吹过湖面，泛起层层涟漪；远处的山峰笼罩在薄雾中，若隐若现。',
      '生活不止眼前的苟且，还有诗和远方的田野。你赤手空拳来到人世间，为找到那片海不顾一切。愿你出走半生，归来仍是少年。',
      '时间是一只藏在黑暗中的温柔的手，在你一出神、一恍惚之间，物走星移。岁月不居，时节如流。',
      '世界上最宽阔的是海洋，比海洋更宽阔的是天空，比天空更宽阔的是人的胸怀。海纳百川，有容乃大。'
    ]
  },
  en: {
    easy: [
      'The quick brown fox jumps over the lazy dog',
      'Life is what happens to you while you are busy making other plans',
      'The only way to do great work is to love what you do',
      'Success is not final failure is not fatal it is the courage to continue that counts'
    ],
    medium: [
      'The quick brown fox jumps over the lazy dog near the river bank on a sunny afternoon.',
      'Life is what happens to you while you are busy making other plans. Keep moving forward.',
      'The only way to do great work is to love what you do and keep learning every single day.',
      'Success is not final, failure is not fatal: it is the courage to continue that counts.'
    ],
    hard: [
      'The quick brown fox jumps over the lazy dog near the river bank on a sunny afternoon; the birds are singing.',
      'Life is what happens to you while you are busy making other plans. Keep moving forward, stay focused.',
      'The only way to do great work is to love what you do and keep learning every single day; never give up.',
      'Success is not final, failure is not fatal: it is the courage to continue that counts; perseverance is key.'
    ]
  }
};

// 随机单词库
const wordPool = {
  zh: ['春天', '阳光', '小鸟', '歌唱', '微风', '湖面', '涟漪', '山峰', '薄雾', '生活', '诗', '远方', '田野', '时间', '黑暗', '温柔', '海洋', '天空', '胸怀', '岁月', '时节', '海纳百川', '出走半生', '归来少年', '赤手空拳', '不顾一切', '若隐若现', '物走星移', '不居如流', '有容乃大'],
  en: ['the', 'quick', 'brown', 'fox', 'jumps', 'over', 'lazy', 'dog', 'life', 'what', 'happens', 'you', 'while', 'busy', 'making', 'other', 'plans', 'only', 'way', 'great', 'work', 'love', 'keep', 'learning', 'every', 'single', 'day', 'success', 'final', 'failure', 'fatal', 'courage', 'continue', 'counts', 'perseverance', 'key', 'never', 'give', 'up', 'stay', 'focused', 'moving', 'forward']
};

// 选择文本
function pickText() {
  if (mode === 'random') {
    const pool = wordPool[lang];
    const count = difficulty === 'easy' ? 10 : difficulty === 'medium' ? 20 : 30;
    let result = [];
    for (let i = 0; i < count; i++) {
      result.push(pool[Math.floor(Math.random() * pool.length)]);
    }
    return lang === 'zh' ? result.join('') : result.join(' ');
  }
  const pool = texts[lang][difficulty];
  return pool[Math.floor(Math.random() * pool.length)];
}

// 渲染文本显示
function renderDisplay() {
  if (!started || completed) {
    textDisplay.textContent = target;
    return;
  }
  
  let html = '';
  const val = textInput.value;
  for (let i = 0; i < target.length; i++) {
    let cls = 'untyped';
    if (i < val.length) {
      cls = val[i] === target[i] ? 'correct' : 'incorrect';
    } else if (i === val.length) {
      cls = 'current';
    }
    html += `<span class="${cls}">${target[i]}</span>`;
  }
  textDisplay.innerHTML = html;
}

// 更新统计
function updateStats() {
  if (!started || completed) return;
  
  const elapsed = (Date.now() - startTime) / 60000;
  if (elapsed < 0.01) return;
  
  const val = textInput.value;
  let correct = 0;
  for (let i = 0; i < Math.min(val.length, target.length); i++) {
    if (val[i] === target[i]) correct++;
  }
  
  const wpm = lang === 'zh' 
    ? Math.round(correct / elapsed)
    : Math.round((correct / 5) / elapsed);
  
  wpmEl.textContent = wpm;
  wpmUnitEl.textContent = lang === 'zh' ? '字/分' : 'WPM';
  
  const acc = val.length > 0 ? Math.round(correct / val.length * 100) : 100;
  accuracyEl.textContent = acc + '%';
  
  timerEl.textContent = Math.round((Date.now() - startTime) / 1000) + 's';
}

// 检查完成
function checkComplete() {
  if (mode === 'countdown') return; // 倒计时模式由定时器结束
  
  if (textInput.value.length >= target.length) {
    completed = true;
    clearInterval(timer);
    showResults();
  }
}

// 显示结果
function showResults() {
  const elapsed = (Date.now() - startTime) / 60000;
  let correct = 0;
  for (let i = 0; i < target.length; i++) {
    if (textInput.value[i] === target[i]) correct++;
  }
  
  const wpm = lang === 'zh' 
    ? Math.round(correct / elapsed)
    : Math.round((correct / 5) / elapsed);
  
  const acc = Math.round(correct / target.length * 100);
  
  wpmEl.textContent = wpm;
  accuracyEl.textContent = acc + '%';
  timerEl.textContent = Math.round((Date.now() - startTime) / 1000) + 's';
  
  textInput.disabled = true;
  renderDisplay();
  
  // 保存历史
  saveHistory(wpm, acc);
}

// 开始测试
function start() {
  target = pickText();
  started = true;
  completed = false;
  textInput.value = '';
  textInput.disabled = false;
  textInput.focus();
  
  wpmEl.textContent = '0';
  accuracyEl.textContent = '--';
  timerEl.textContent = '0s';
  
  startTime = Date.now();
  timer = setInterval(updateStats, 200);
  
  renderDisplay();
  
  // 倒计时模式
  if (mode === 'countdown') {
    setTimeout(() => {
      if (started && !completed) {
        completed = true;
        clearInterval(timer);
        showResults();
      }
    }, 60000);
  }
}

// 保存历史
function saveHistory(wpm, acc) {
  const history = JSON.parse(localStorage.getItem('typingHistory') || '[]');
  history.unshift({
    id: new Date().toISOString(),
    date: new Date().toISOString(),
    mode: mode,
    lang: lang,
    difficulty: difficulty,
    wpm: wpm,
    accuracy: acc,
    duration: Math.round((Date.now() - startTime) / 1000),
    correctChars: wpm,
    totalChars: target.length
  });
  
  // 只保留最近20条
  if (history.length > 20) history.length = 20;
  
  localStorage.setItem('typingHistory', JSON.stringify(history));
  renderHistory();
  renderLeaderboard();
}

// 渲染历史记录
function renderHistory() {
  const history = JSON.parse(localStorage.getItem('typingHistory') || '[]');
  const historyList = document.getElementById('historyList');
  
  if (history.length === 0) {
    historyList.innerHTML = '<div style="text-align:center;color:#94a3b8;padding:20px;">暂无记录</div>';
    return;
  }
  
  historyList.innerHTML = history.map(item => `
    <div class="history-item">
      <div>
        <div style="font-weight:600;">${item.wpm} ${item.lang === 'zh' ? '字/分' : 'WPM'}</div>
        <div style="font-size:11px;color:#94a3b8;">${new Date(item.date).toLocaleDateString()} · ${item.mode === 'text' ? '按文本' : item.mode === 'countdown' ? '60秒' : '随机'} · ${item.accuracy}%</div>
      </div>
      <div style="font-size:12px;color:#64748b;">${item.duration}s</div>
    </div>
  `).join('');
}

// 渲染排行榜
function renderLeaderboard() {
  const history = JSON.parse(localStorage.getItem('typingHistory') || '[]');
  const leaderboard = document.getElementById('leaderboardList');
  
  const sorted = [...history].sort((a, b) => b.wpm - a.wpm).slice(0, 10);
  
  if (sorted.length === 0) {
    leaderboard.innerHTML = '<div style="text-align:center;color:#94a3b8;padding:20px;">暂无记录</div>';
    return;
  }
  
  leaderboard.innerHTML = sorted.map((item, i) => `
    <div class="leaderboard-item">
      <div>
        <div style="font-weight:600;">#${i + 1} ${item.wpm} ${item.lang === 'zh' ? '字/分' : 'WPM'}</div>
        <div style="font-size:11px;color:#94a3b8;">${new Date(item.date).toLocaleDateString()} · ${item.accuracy}%</div>
      </div>
      <div style="font-size:12px;color:#64748b;">${item.duration}s</div>
    </div>
  `).join('');
}

// 事件监听
textInput.addEventListener('input', () => {
  if (started && !completed) {
    renderDisplay();
    checkComplete();
  }
});

// 按钮事件
document.querySelectorAll('.lang-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    lang = btn.dataset.lang;
    document.querySelectorAll('.lang-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    if (started) {
      clearInterval(timer);
      started = false;
    }
    textInput.value = '';
    textInput.disabled = true;
    wpmEl.textContent = '0';
    accuracyEl.textContent = '--';
    timerEl.textContent = '0s';
    target = pickText();
    renderDisplay();
  });
});

document.querySelectorAll('.mode-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    mode = btn.dataset.mode;
    document.querySelectorAll('.mode-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    if (started) {
      clearInterval(timer);
      started = false;
    }
    textInput.value = '';
    textInput.disabled = true;
    wpmEl.textContent = '0';
    accuracyEl.textContent = '--';
    timerEl.textContent = '0s';
    target = pickText();
    renderDisplay();
  });
});

document.querySelectorAll('.diff-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    difficulty = btn.dataset.diff;
    document.querySelectorAll('.diff-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    if (started) {
      clearInterval(timer);
      started = false;
    }
    textInput.value = '';
    textInput.disabled = true;
    wpmEl.textContent = '0';
    accuracyEl.textContent = '--';
    timerEl.textContent = '0s';
    target = pickText();
    renderDisplay();
  });
});

// 高级面板切换
document.addEventListener('keydown', (e) => {
  if (e.key === 'Tab' && !started) {
    e.preventDefault();
    panelVisible = !panelVisible;
    advancedPanel.classList.toggle('visible', panelVisible);
  }
  if (e.key === 'Escape' && panelVisible) {
    panelVisible = false;
    advancedPanel.classList.remove('visible');
  }
  if (e.key === 'Enter' && !started) {
    start();
  }
});

// 面板标签切换
document.querySelectorAll('.panel-tab').forEach(tab => {
  tab.addEventListener('click', () => {
    document.querySelectorAll('.panel-tab').forEach(t => t.classList.remove('active'));
    document.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
    tab.classList.add('active');
    document.getElementById(tab.dataset.tab + 'Tab').classList.add('active');
  });
});

// 清除历史
document.getElementById('clearHistory').addEventListener('click', () => {
  localStorage.removeItem('typingHistory');
  renderHistory();
  renderLeaderboard();
});

// 应用自定义文本
document.getElementById('applyCustom').addEventListener('click', () => {
  const customText = document.getElementById('customText').value.trim();
  if (customText) {
    target = customText;
    if (started) {
      clearInterval(timer);
      started = false;
    }
    textInput.value = '';
    textInput.disabled = true;
    wpmEl.textContent = '0';
    accuracyEl.textContent = '--';
    timerEl.textContent = '0s';
    renderDisplay();
    
    // 关闭面板
    panelVisible = false;
    advancedPanel.classList.remove('visible');
  }
});

// 初始化
target = pickText();
renderDisplay();
renderHistory();
renderLeaderboard();
```

- [ ] **Step 2: 验证核心功能**

在浏览器中打开 `typing-test.html`，测试：
- 点击"开始测试"按钮（或按 Enter）能开始打字
- 输入文字时实时显示颜色反馈
- WPM、准确率、时间实时更新
- 输入完成后自动停止并显示结果

---

### Task 4: 模式切换功能

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 测试模式切换**

在浏览器中测试三种模式：
- **按文本打字**：输入完文本后自动结束
- **60秒计时**：60秒后自动结束
- **随机单词**：持续打字直到手动停止（按 Enter 重新开始）

验证要点：
- 切换模式后重置状态
- 每种模式都能正常开始和结束
- 倒计时模式正确计时

- [ ] **Step 2: 修复模式切换 bug**

如果发现问题，修复代码。常见问题：
- 切换模式后文本不更新
- 倒计时模式不自动结束
- 随机单词模式文本长度不正确

---

### Task 5: 难度选择功能

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 测试难度切换**

在浏览器中测试三种难度：
- **简单**：短句、无标点
- **中等**：有标点、中等长度
- **困难**：长词、复杂标点

验证要点：
- 切换难度后文本内容变化
- 每种难度的文本符合预期
- 中英文难度对应正确

- [ ] **Step 2: 补充文本数据**

如果某种难度的文本太少，补充更多文本：
- 每种难度每种语言至少 4 段文本
- 确保文本质量，无错别字

---

### Task 6: 历史记录和排行榜

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 测试历史记录**

完成几次打字测试后：
- 按 Tab 打开高级面板
- 查看历史记录标签
- 验证记录是否正确保存
- 验证记录是否按时间倒序排列

- [ ] **Step 2: 测试排行榜**

- 切换到排行榜标签
- 验证是否按速度排序
- 验证是否只显示前 10 条
- 验证清除历史后排行榜是否清空

- [ ] **Step 3: 测试自定义文本**

- 切换到自定义文本标签
- 输入自定义文本
- 点击"应用"按钮
- 验证文本是否正确应用
- 验证打字功能是否正常

---

### Task 7: 响应式适配

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 测试移动端布局**

使用浏览器开发者工具模拟移动设备：
- 测试 375px 宽度（iPhone SE）
- 测试 768px 宽度（iPad）

验证要点：
- 控制栏是否正确换行
- 按钮是否易于点击
- 文本是否易于阅读
- 输入框是否易于操作

- [ ] **Step 2: 测试深色模式**

切换系统深色模式：
- 验证背景颜色是否正确
- 验证文字颜色是否正确
- 验证按钮样式是否正确
- 验证输入框样式是否正确

---

### Task 8: 快捷键和防误触

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 测试快捷键**

- 测试 Enter 键：重新开始
- 测试 Tab 键：展开/收起高级面板
- 测试 Escape 键：收起高级面板

验证要点：
- 打字过程中 Tab 键被禁用
- 打字过程中 Escape 键被禁用
- 面板展开时按 Escape 能正确收起

- [ ] **Step 2: 测试防误触**

- 打字过程中点击页面其他区域
- 验证输入框是否保持焦点
- 验证打字是否被中断

---

### Task 9: 最终测试和提交

**Files:**
- Modify: `typing-test.html`

- [ ] **Step 1: 完整功能测试**

按照设计文档中的验证标准逐项测试：

1. 核心打字功能正常（中英文、实时反馈、WPM/准确率）
2. 三种模式均可正常切换和使用
3. 难度选择正常工作
4. 历史记录正确保存和显示
5. 排行榜正确排序
6. 自定义文本正常工作
7. 响应式布局在手机/平板/桌面端均正常
8. 深色模式自动切换
9. 快捷键正常工作
10. 无控制台错误

- [ ] **Step 2: 性能检查**

- 检查是否有内存泄漏（定时器是否正确清除）
- 检查 localStorage 是否正确读写
- 检查页面加载速度

- [ ] **Step 3: 代码清理**

- 移除调试代码
- 移除未使用的变量
- 确保代码格式一致

- [ ] **Step 4: 提交代码**

```bash
git add typing-test.html
git commit -m "feat: rewrite typing test with minimalist design

- Add three modes: text, countdown, random
- Add difficulty levels: easy, medium, hard
- Add history and leaderboard with localStorage
- Add custom text support
- Add keyboard shortcuts (Enter, Tab, Escape)
- Add responsive design for mobile/tablet
- Add dark mode support
- Fix accuracy calculation inconsistency
- Fix mode switching bug"
```
