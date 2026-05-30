# 记忆翻牌游戏改进实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 对记忆翻牌游戏进行全面改进，增加动画特效、游戏功能和音效反馈

**Architecture:** 渐进式改进，在现有单文件 HTML 游戏基础上添加新功能，保持代码组织清晰

**Tech Stack:** HTML5, CSS3 (3D transforms, animations), JavaScript (Web Audio API, localStorage)

---

## 文件结构

- **Modify:** `memory.html` - 主游戏文件（所有改动都在此文件）

---

## Task 1: 添加翻牌动画

**Files:**
- Modify: `memory.html:8-31` (CSS 部分)
- Modify: `memory.html:18-22` (卡片样式)

- [ ] **Step 1: 添加 3D 翻转动画的 CSS 基础结构**

在 `<style>` 标签中添加以下 CSS：

```css
.card {
  aspect-ratio: 1;
  border-radius: 12px;
  cursor: pointer;
  transition: transform 0.3s ease-in-out;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  font-weight: 700;
  position: relative;
  transform-style: preserve-3d;
  perspective: 1000px;
}

.card .card-front,
.card .card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 12px;
}

.card .card-front {
  background: linear-gradient(135deg, #818cf8, #6366f1);
  box-shadow: 0 4px 12px rgba(99, 102, 241, 0.25);
  color: transparent;
}

.card .card-back {
  background: #fff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
  color: #1e293b;
  transform: rotateY(180deg);
}

.card.flipped {
  transform: rotateY(180deg);
}

.card.matched {
  background: #d1fae5;
  color: #059669;
  pointer-events: none;
  box-shadow: 0 2px 8px rgba(16, 185, 129, 0.15);
}
```

- [ ] **Step 2: 更新 JavaScript 中的卡片创建逻辑**

修改 `init()` 函数中的卡片创建部分：

```javascript
function init() {
  // ... 现有代码 ...
  grid.innerHTML = '';
  deck.forEach((v, i) => {
    const c = document.createElement('div');
    c.className = 'card';
    c.dataset.index = i;
    c.dataset.value = v;
    
    const front = document.createElement('div');
    front.className = 'card-front';
    front.textContent = '?';
    
    const back = document.createElement('div');
    back.className = 'card-back';
    back.textContent = EMOJIS[parseInt(v)];
    
    c.appendChild(front);
    c.appendChild(back);
    c.addEventListener('click', () => flip(c));
    grid.appendChild(c);
  });
}
```

- [ ] **Step 3: 更新翻牌逻辑使用新的 CSS 类**

修改 `flip()` 函数：

```javascript
function flip(card) {
  if (locked || card.classList.contains('flipped') || card.classList.contains('matched')) return;
  if (!timerInt) {
    timerInt = setInterval(() => { seconds++; timerEl.textContent = seconds }, 1000);
  }
  card.classList.add('flipped');
  flipped.push(card);
  
  if (flipped.length === 2) {
    moves++;
    movesEl.textContent = moves;
    const [a, b] = flipped;
    if (a.dataset.value === b.dataset.value) {
      a.classList.add('matched');
      b.classList.add('matched');
      matched++;
      pairsEl.textContent = matched + '/' + (total / 2);
      flipped = [];
      if (matched === total / 2) win();
    } else {
      locked = true;
      setTimeout(() => {
        a.classList.remove('flipped');
        b.classList.remove('flipped');
        flipped = [];
        locked = false;
      }, 600);
    }
  }
}
```

- [ ] **Step 4: 测试翻牌动画**

打开浏览器测试：
1. 点击卡片，应该看到 3D 翻转效果
2. 翻开两张不匹配的卡片，应该看到它们翻回去
3. 翻开两张匹配的卡片，应该保持翻开状态并显示绿色

- [ ] **Step 5: 提交代码**

```bash
git add memory.html
git commit -m "feat: add 3D card flip animation"
```

---

## Task 2: 添加匹配成功特效

**Files:**
- Modify: `memory.html` (CSS 动画)
- Modify: `memory.html` (JavaScript 粒子效果)

- [ ] **Step 1: 添加弹跳动画 CSS**

在 `<style>` 标签中添加：

```css
@keyframes bounce {
  0%, 100% { transform: rotateY(180deg) scale(1); }
  50% { transform: rotateY(180deg) scale(1.1); }
}

.card.matched {
  animation: bounce 0.5s ease-in-out;
}

@keyframes particle {
  0% { opacity: 1; transform: translate(0, 0) scale(1); }
  100% { opacity: 0; transform: translate(var(--tx), var(--ty)) scale(0); }
}

.particle {
  position: absolute;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  pointer-events: none;
  animation: particle 0.8s ease-out forwards;
}
```

- [ ] **Step 2: 创建粒子效果函数**

在 JavaScript 部分添加：

```javascript
function createParticles(x, y) {
  const colors = ['#10b981', '#34d399', '#6ee7b7', '#a7f3d0'];
  for (let i = 0; i < 12; i++) {
    const particle = document.createElement('div');
    particle.className = 'particle';
    particle.style.left = x + 'px';
    particle.style.top = y + 'px';
    particle.style.background = colors[Math.floor(Math.random() * colors.length)];
    particle.style.setProperty('--tx', (Math.random() - 0.5) * 100 + 'px');
    particle.style.setProperty('--ty', (Math.random() - 0.5) * 100 + 'px');
    document.body.appendChild(particle);
    setTimeout(() => particle.remove(), 800);
  }
}
```

- [ ] **Step 3: 在匹配成功时触发粒子效果**

修改 `flip()` 函数中的匹配成功部分：

```javascript
if (a.dataset.value === b.dataset.value) {
  a.classList.add('matched');
  b.classList.add('matched');
  
  // 获取卡片位置并创建粒子效果
  const rect = a.getBoundingClientRect();
  createParticles(rect.left + rect.width / 2, rect.top + rect.height / 2);
  
  matched++;
  pairsEl.textContent = matched + '/' + (total / 2);
  flipped = [];
  if (matched === total / 2) win();
}
```

- [ ] **Step 4: 测试匹配成功特效**

测试步骤：
1. 翻开两张匹配的卡片
2. 应该看到卡片弹跳动画
3. 应该看到粒子从卡片位置向四周飘散

- [ ] **Step 5: 提交代码**

```bash
git add memory.html
git commit -m "feat: add match success particles and bounce animation"
```

---

## Task 3: 添加游戏胜利庆祝效果

**Files:**
- Modify: `memory.html` (CSS 纸屑动画)
- Modify: `memory.html` (JavaScript 庆祝逻辑)

- [ ] **Step 1: 添加纸屑掉落动画 CSS**

在 `<style>` 标签中添加：

```css
@keyframes confetti-fall {
  0% { transform: translateY(-100vh) rotate(0deg); opacity: 1; }
  100% { transform: translateY(100vh) rotate(720deg); opacity: 0; }
}

.confetti {
  position: fixed;
  width: 10px;
  height: 10px;
  top: -10px;
  pointer-events: none;
  z-index: 1000;
  animation: confetti-fall 3s ease-in forwards;
}

.win-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
  opacity: 0;
  transition: opacity 0.3s;
}

.win-overlay.show {
  opacity: 1;
}

.win-card {
  background: white;
  border-radius: 20px;
  padding: 40px;
  text-align: center;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  transform: scale(0.8);
  transition: transform 0.3s;
}

.win-overlay.show .win-card {
  transform: scale(1);
}
```

- [ ] **Step 2: 创建纸屑生成函数**

在 JavaScript 部分添加：

```javascript
function createConfetti() {
  const colors = ['#ff6b6b', '#feca57', '#48dbfb', '#ff9ff3', '#54a0ff', '#5f27cd'];
  for (let i = 0; i < 50; i++) {
    const confetti = document.createElement('div');
    confetti.className = 'confetti';
    confetti.style.left = Math.random() * 100 + 'vw';
    confetti.style.background = colors[Math.floor(Math.random() * colors.length)];
    confetti.style.animationDelay = Math.random() * 2 + 's';
    confetti.style.animationDuration = (Math.random() * 2 + 2) + 's';
    document.body.appendChild(confetti);
    setTimeout(() => confetti.remove(), 5000);
  }
}
```

- [ ] **Step 3: 创建胜利弹窗函数**

在 JavaScript 部分添加：

```javascript
function showWinDialog() {
  const overlay = document.createElement('div');
  overlay.className = 'win-overlay';
  
  const card = document.createElement('div');
  card.className = 'win-card';
  card.innerHTML = `
    <div style="font-size: 48px; margin-bottom: 15px;">🎉</div>
    <h2 style="font-size: 24px; margin-bottom: 10px; color: #1e293b;">恭喜通关！</h2>
    <p style="font-size: 16px; color: #64748b; margin-bottom: 20px;">
      步数: ${moves} | 用时: ${seconds}秒
    </p>
    <button onclick="this.closest('.win-overlay').remove()" 
            style="padding: 12px 30px; background: linear-gradient(135deg, #818cf8, #6366f1); 
                   color: white; border: none; border-radius: 12px; font-size: 16px; 
                   font-weight: 600; cursor: pointer;">
      再来一局
    </button>
  `;
  
  overlay.appendChild(card);
  document.body.appendChild(overlay);
  
  setTimeout(() => overlay.classList.add('show'), 10);
}
```

- [ ] **Step 4: 修改 win() 函数使用新的庆祝效果**

修改 `win()` 函数：

```javascript
function win() {
  if (timerInt) clearInterval(timerInt);
  
  // 保存最佳成绩
  const best = loadBest();
  if (!best || moves < best.moves || (moves === best.moves && seconds < best.seconds)) {
    saveBest({ moves, seconds, date: Date.now() });
  }
  
  // 显示庆祝效果
  createConfetti();
  showWinDialog();
  
  gradeText.textContent = '完成! ' + moves + ' 步 · ' + seconds + ' 秒';
}
```

- [ ] **Step 5: 测试游戏胜利庆祝**

测试步骤：
1. 完成一局游戏
2. 应该看到全屏纸屑掉落
3. 应该看到胜利弹窗显示成绩
4. 点击"再来一局"应该关闭弹窗并重新开始

- [ ] **Step 6: 提交代码**

```bash
git add memory.html
git commit -m "feat: add win celebration with confetti and dialog"
```

---

## Task 4: 添加提示功能

**Files:**
- Modify: `memory.html` (HTML 按钮)
- Modify: `memory.html` (JavaScript 提示逻辑)

- [ ] **Step 1: 添加提示按钮 HTML**

在 `<div class="controls-row">` 后面添加：

```html
<div class="controls-row">
  <button class="btn" id="hintBtn">💡 提示 (3)</button>
  <button class="btn" id="resetBtn">重新开始</button>
</div>
```

- [ ] **Step 2: 添加提示按钮样式 CSS**

在 `<style>` 标签中添加：

```css
.btn-hint {
  background: linear-gradient(135deg, #fbbf24, #f59e0b);
  color: white;
  border: none;
}

.btn-hint:hover {
  background: linear-gradient(135deg, #f59e0b, #d97706);
  color: white;
}

.btn-hint:disabled {
  background: #d1d5db;
  cursor: not-allowed;
}
```

- [ ] **Step 3: 添加提示功能 JavaScript**

在 JavaScript 部分添加：

```javascript
let hintsLeft = 3;
const hintBtn = document.getElementById('hintBtn');

function useHint() {
  if (hintsLeft <= 0 || locked) return;
  
  hintsLeft--;
  hintBtn.textContent = `💡 提示 (${hintsLeft})`;
  if (hintsLeft === 0) hintBtn.disabled = true;
  
  // 短暂显示所有卡牌
  const cards = document.querySelectorAll('.card:not(.matched)');
  cards.forEach(card => {
    card.classList.add('flipped');
  });
  
  locked = true;
  setTimeout(() => {
    cards.forEach(card => {
      if (!card.classList.contains('matched')) {
        card.classList.remove('flipped');
      }
    });
    locked = false;
  }, 1000);
}

hintBtn.addEventListener('click', useHint);
```

- [ ] **Step 4: 在 init() 函数中重置提示次数**

在 `init()` 函数中添加：

```javascript
hintsLeft = 3;
hintBtn.textContent = '💡 提示 (3)';
hintBtn.disabled = false;
```

- [ ] **Step 5: 测试提示功能**

测试步骤：
1. 点击提示按钮，所有卡牌应该短暂翻开 1 秒
2. 提示次数应该减少
3. 用完 3 次后按钮应该禁用
4. 重新开始游戏后提示次数应该重置

- [ ] **Step 6: 提交代码**

```bash
git add memory.html
git commit -m "feat: add hint feature with 3 uses per game"
```

---

## Task 5: 添加排行榜系统

**Files:**
- Modify: `memory.html` (JavaScript 存储逻辑)
- Modify: `memory.html` (HTML 排行榜显示)

- [ ] **Step 1: 更新存储结构**

修改 `loadBest()` 和 `saveBest()` 函数：

```javascript
const STORAGE_KEY = 'memory_best';

function loadBest() {
  const v = localStorage.getItem(STORAGE_KEY);
  return v ? JSON.parse(v) : { easy: null, medium: null, hard: null };
}

function saveBest(level, record) {
  const best = loadBest();
  if (!best[level] || record.moves < best[level].moves || 
      (record.moves === best[level].moves && record.seconds < best[level].seconds)) {
    best[level] = record;
    localStorage.setItem(STORAGE_KEY, JSON.stringify(best));
    return true;
  }
  return false;
}
```

- [ ] **Step 2: 创建排行榜显示函数**

在 JavaScript 部分添加：

```javascript
function showLeaderboard() {
  const best = loadBest();
  const overlay = document.createElement('div');
  overlay.className = 'win-overlay';
  
  const card = document.createElement('div');
  card.className = 'win-card';
  card.style.maxWidth = '400px';
  
  let html = `
    <h2 style="font-size: 20px; margin-bottom: 20px; color: #1e293b;">🏆 排行榜</h2>
    <div style="text-align: left;">
  `;
  
  const levels = [
    { key: 'easy', name: '简单 (4×3)' },
    { key: 'medium', name: '普通 (4×4)' },
    { key: 'hard', name: '困难 (6×4)' }
  ];
  
  levels.forEach(level => {
    const data = best[level.key];
    html += `
      <div style="display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #e2e8f0;">
        <span style="font-weight: 600; color: #1e293b;">${level.name}</span>
        <span style="color: ${data ? '#10b981' : '#94a3b8'};">
          ${data ? `${data.moves}步 / ${data.seconds}秒` : '暂无记录'}
        </span>
      </div>
    `;
  });
  
  html += `
    </div>
    <button onclick="this.closest('.win-overlay').remove()" 
            style="margin-top: 20px; padding: 10px 25px; background: #818cf8; 
                   color: white; border: none; border-radius: 10px; cursor: pointer;">
      关闭
    </button>
  `;
  
  card.innerHTML = html;
  overlay.appendChild(card);
  document.body.appendChild(overlay);
  
  setTimeout(() => overlay.classList.add('show'), 10);
}
```

- [ ] **Step 3: 添加排行榜按钮**

在 HTML 中添加：

```html
<button class="btn" id="leaderboardBtn">🏆 排行榜</button>
```

在 JavaScript 中添加：

```javascript
const leaderboardBtn = document.getElementById('leaderboardBtn');
leaderboardBtn.addEventListener('click', showLeaderboard);
```

- [ ] **Step 4: 更新 win() 函数使用新的存储结构**

修改 `win()` 函数：

```javascript
function win() {
  if (timerInt) clearInterval(timerInt);
  
  const level = levelSelect.value;
  const isNewBest = saveBest(level, { moves, seconds, date: Date.now() });
  
  createConfetti();
  showWinDialog(isNewBest);
  
  gradeText.textContent = '完成! ' + moves + ' 步 · ' + seconds + ' 秒';
}
```

- [ ] **Step 5: 更新胜利弹窗显示是否打破记录**

修改 `showWinDialog()` 函数：

```javascript
function showWinDialog(isNewBest) {
  // ... 现有代码 ...
  const bestText = isNewBest ? '🎊 新纪录！' : '';
  card.innerHTML = `
    <div style="font-size: 48px; margin-bottom: 15px;">🎉</div>
    <h2 style="font-size: 24px; margin-bottom: 5px; color: #1e293b;">恭喜通关！</h2>
    ${bestText ? `<p style="font-size: 18px; color: #10b981; margin-bottom: 10px;">${bestText}</p>` : ''}
    <p style="font-size: 16px; color: #64748b; margin-bottom: 20px;">
      步数: ${moves} | 用时: ${seconds}秒
    </p>
    <button onclick="this.closest('.win-overlay').remove(); init();" 
            style="padding: 12px 30px; background: linear-gradient(135deg, #818cf8, #6366f1); 
                   color: white; border: none; border-radius: 12px; font-size: 16px; 
                   font-weight: 600; cursor: pointer;">
      再来一局
    </button>
  `;
  // ... 现有代码 ...
}
```

- [ ] **Step 6: 测试排行榜功能**

测试步骤：
1. 完成一局游戏，查看是否显示"新纪录"
2. 点击排行榜按钮，查看成绩是否正确记录
3. 用更差的成绩完成游戏，查看是否更新记录
4. 刷新页面，查看记录是否持久化

- [ ] **Step 7: 提交代码**

```bash
git add memory.html
git commit -m "feat: add leaderboard system with best scores per difficulty"
```

---

## Task 6: 添加主题切换功能

**Files:**
- Modify: `memory.html` (HTML 主题选择器)
- Modify: `memory.html` (JavaScript 主题管理)

- [ ] **Step 1: 定义主题数据**

在 JavaScript 部分添加：

```javascript
const THEMES = {
  fruits: {
    name: '🍎 水果',
    emojis: ['🍎','🍊','🍋','🍇','🍓','🍒','🥝','🍑','🥕','🌽','🍕','🎂','⚽','🎸','🚀','🌈','🐶','🐱','🦊','🐼','🐸','🦋','🌸','⭐']
  },
  animals: {
    name: '🐶 动物',
    emojis: ['🐶','🐱','🦊','🐼','🐸','🦋','🦁','🐯','🐻','🐨','🐵','🐔','🐧','🐴','🦄','🐙','🐝','🐞','🐢','🐙','🦑','🦐','🦞','🦀']
  },
  emojis: {
    name: '😀 表情',
    emojis: ['😀','😁','😂','🤣','😃','😄','😅','😆','😉','😊','😋','😎','😍','😘','🥰','😗','😙','🥲','😚','🙂','🤗','🤩','🥳','😏']
  },
  sports: {
    name: '⚽ 运动',
    emojis: ['⚽','🏀','🏈','⚾','🎾','🏐','🏉','🎱','🏓','🏸','🥊','🥋','⛳','🏆','🥇','🥈','🥉','🎯','🎳','♟️','🎿','🛷','🥅','🎪']
  }
};

let currentTheme = localStorage.getItem('memory_theme') || 'fruits';
```

- [ ] **Step 2: 添加主题选择器 HTML**

在 `<div class="controls-row">` 中添加：

```html
<label>主题 <select id="themeSelect">
  <option value="fruits">🍎 水果</option>
  <option value="animals">🐶 动物</option>
  <option value="emojis">😀 表情</option>
  <option value="sports">⚽ 运动</option>
</select></label>
```

- [ ] **Step 3: 添加主题切换逻辑**

在 JavaScript 部分添加：

```javascript
const themeSelect = document.getElementById('themeSelect');
themeSelect.value = currentTheme;

themeSelect.addEventListener('change', function() {
  currentTheme = this.value;
  localStorage.setItem('memory_theme', currentTheme);
  init();
});
```

- [ ] **Step 4: 修改 init() 函数使用当前主题**

修改 `init()` 函数中的 deck 创建部分：

```javascript
function init() {
  setLevel(levelSelect.value);
  flipped = [];
  matched = 0;
  moves = 0;
  seconds = 0;
  locked = false;
  hintsLeft = 3;
  
  if (timerInt) clearInterval(timerInt);
  timerInt = null;
  
  movesEl.textContent = '0';
  pairsEl.textContent = '0/' + (total / 2);
  timerEl.textContent = '0';
  gradeText.textContent = '';
  hintBtn.textContent = '💡 提示 (3)';
  hintBtn.disabled = false;
  
  const theme = THEMES[currentTheme];
  const pairs = total / 2;
  const deck = shuffle([...Array(pairs)].map((_, i) => i).concat([...Array(pairs)].map((_, i) => i)));
  
  grid.style.gridTemplateColumns = `repeat(${cols}, 1fr)`;
  const size = Math.min(72, (window.innerWidth - 40) / cols - 8);
  grid.innerHTML = '';
  
  deck.forEach((v, i) => {
    const c = document.createElement('div');
    c.className = 'card';
    c.dataset.index = i;
    c.dataset.value = v;
    
    const front = document.createElement('div');
    front.className = 'card-front';
    front.textContent = '?';
    
    const back = document.createElement('div');
    back.className = 'card-back';
    back.textContent = theme.emojis[parseInt(v)];
    
    c.appendChild(front);
    c.appendChild(back);
    c.addEventListener('click', () => flip(c));
    grid.appendChild(c);
  });
}
```

- [ ] **Step 5: 测试主题切换功能**

测试步骤：
1. 切换到不同主题，查看卡牌图案是否正确更新
2. 切换主题后游戏应该重新开始
3. 刷新页面，查看主题是否持久化
4. 每个主题都应该有足够的 emoji 来支持所有难度

- [ ] **Step 6: 提交代码**

```bash
git add memory.html
git commit -m "feat: add theme switching with 4 theme options"
```

---

## Task 7: 添加音效系统

**Files:**
- Modify: `memory.html` (JavaScript Web Audio API)

- [ ] **Step 1: 创建音效管理器**

在 JavaScript 部分添加：

```javascript
class AudioManager {
  constructor() {
    this.ctx = null;
    this.muted = localStorage.getItem('memory_muted') === 'true';
  }
  
  init() {
    if (!this.ctx) {
      this.ctx = new (window.AudioContext || window.webkitAudioContext)();
    }
  }
  
  playTone(frequency, duration, type = 'sine', volume = 0.3) {
    if (this.muted || !this.ctx) return;
    
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();
    
    osc.type = type;
    osc.frequency.value = frequency;
    gain.gain.value = volume;
    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + duration);
    
    osc.connect(gain);
    gain.connect(this.ctx.destination);
    
    osc.start();
    osc.stop(this.ctx.currentTime + duration);
  }
  
  playFlip() {
    this.playTone(800, 0.1, 'sine', 0.2);
  }
  
  playMatch() {
    this.playTone(523, 0.15, 'sine', 0.3);
    setTimeout(() => this.playTone(659, 0.15, 'sine', 0.3), 150);
  }
  
  playMismatch() {
    this.playTone(200, 0.3, 'sawtooth', 0.15);
  }
  
  playWin() {
    const notes = [523, 659, 784, 1047];
    notes.forEach((note, i) => {
      setTimeout(() => this.playTone(note, 0.3, 'sine', 0.3), i * 200);
    });
  }
  
  toggleMute() {
    this.muted = !this.muted;
    localStorage.setItem('memory_muted', this.muted);
    return this.muted;
  }
}

const audio = new AudioManager();
```

- [ ] **Step 2: 添加静音按钮 HTML**

在 `<div class="container">` 开头添加：

```html
<button class="btn-mute" id="muteBtn" style="position: absolute; top: 10px; right: 10px; 
        background: none; border: none; font-size: 20px; cursor: pointer; padding: 5px;">
  🔊
</button>
```

- [ ] **Step 3: 添加静音按钮逻辑**

在 JavaScript 部分添加：

```javascript
const muteBtn = document.getElementById('muteBtn');
muteBtn.textContent = audio.muted ? '🔇' : '🔊';

muteBtn.addEventListener('click', function() {
  audio.init();
  const muted = audio.toggleMute();
  this.textContent = muted ? '🔇' : '🔊';
});
```

- [ ] **Step 4: 在游戏交互中添加音效**

修改 `flip()` 函数：

```javascript
function flip(card) {
  if (locked || card.classList.contains('flipped') || card.classList.contains('matched')) return;
  
  audio.init();
  audio.playFlip();
  
  // ... 现有代码 ...
  
  if (a.dataset.value === b.dataset.value) {
    audio.playMatch();
    // ... 匹配成功代码 ...
  } else {
    audio.playMismatch();
    // ... 匹配失败代码 ...
  }
}
```

修改 `win()` 函数：

```javascript
function win() {
  // ... 现有代码 ...
  audio.playWin();
  // ... 现有代码 ...
}
```

- [ ] **Step 5: 测试音效功能**

测试步骤：
1. 翻牌时应该听到"叮"声
2. 匹配成功应该听到上升音调
3. 匹配失败应该听到低沉"嗡"声
4. 游戏胜利应该听到欢快旋律
5. 点击静音按钮应该关闭/开启音效
6. 刷新页面后静音状态应该保持

- [ ] **Step 6: 提交代码**

```bash
git add memory.html
git commit -m "feat: add audio system with Web Audio API and mute control"
```

---

## Task 8: 最终整合与测试

**Files:**
- Modify: `memory.html` (最终调整)

- [ ] **Step 1: 更新响应式样式**

在 `<style>` 标签的 `@media` 部分添加：

```css
@media (max-width: 420px) {
  .container {
    padding: 20px 12px 20px;
  }
  
  .card {
    font-size: 22px;
  }
  
  .grid {
    gap: 5px;
  }
  
  .controls-row {
    flex-wrap: wrap;
    gap: 8px;
  }
  
  .btn {
    padding: 8px 16px;
    font-size: 13px;
  }
  
  .btn-mute {
    top: 5px;
    right: 5px;
    font-size: 18px;
  }
}
```

- [ ] **Step 2: 完整功能测试**

测试清单：
1. 翻牌动画流畅
2. 匹配成功有粒子效果
3. 匹配失败有抖动反馈
4. 游戏胜利有纸屑和弹窗
5. 提示功能正常（3次）
6. 排行榜正确记录成绩
7. 主题切换正常
8. 音效正常播放
9. 静音功能正常
10. 响应式布局正常
11. 数据持久化正常

- [ ] **Step 3: 性能优化检查**

检查项：
- 动画是否流畅（60fps）
- 内存泄漏检查（事件监听器清理）
- localStorage 读写是否正常

- [ ] **Step 4: 浏览器兼容性测试**

测试浏览器：
- Chrome (最新版)
- Firefox (最新版)
- Edge (最新版)
- Safari (如果有的话)
- 移动端 Chrome
- 移动端 Safari

- [ ] **Step 5: 最终提交**

```bash
git add memory.html
git commit -m "feat: complete memory game enhancement with animations, features, and audio"
```

---

## 实施顺序建议

建议按以下顺序实施，每个 Task 独立可测试：

1. Task 1: 翻牌动画（基础视觉改进）
2. Task 2: 匹配成功特效（增强反馈）
3. Task 3: 游戏胜利庆祝（完成体验）
4. Task 4: 提示功能（游戏功能）
5. Task 5: 排行榜系统（数据持久化）
6. Task 6: 主题切换（个性化）
7. Task 7: 音效系统（感官体验）
8. Task 8: 最终整合与测试（质量保证）

每个 Task 完成后都可以独立测试，确保功能正常后再进行下一个。