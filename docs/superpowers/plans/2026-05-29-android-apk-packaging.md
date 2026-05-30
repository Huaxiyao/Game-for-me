# Android APK 打包实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将 10 个现有 HTML 游戏打包为单个 Android APK，通过 Capacitor 实现

**Architecture:** Capacitor 多页应用，`www/` 下新建游戏列表页 + 共享返回按钮脚本，每个游戏 HTML 尾部注入 `<script>` 标签加载共享脚本，不修改游戏逻辑代码

**Tech Stack:** Node.js, Capacitor (JS), Android SDK, HTML/CSS/JS

**源文件路径:** `C:\Users\YDN\Desktop\Projects for Open\`

---

### Task 1: 创建 Capacitor 项目骨架

**Files:**
- Create: `www/index.html` (暂时占位，Task 2 会覆盖)
- Create: `capacitor.config.ts`
- Create: `package.json`

- [ ] **Step 1: 初始化 Node 项目**

```powershell
Set-Location -LiteralPath "C:\Users\YDN\Desktop\Projects for Open\brain-trainer"
npm init -y
```

- [ ] **Step 2: 安装 Capacitor 依赖**

```powershell
npm install @capacitor/core @capacitor/cli
```

Expected: 安装成功，`node_modules/` 和 `package-lock.json` 生成

- [ ] **Step 3: 初始化 Capacitor**

```powershell
npx cap init "脑力训练" "com.brain.trainer" --web-dir www
```

Expected: 生成 `capacitor.config.ts`，内容如下：

```typescript
import { CapacitorConfig } from '@capacitor/cli';

const config: CapacitorConfig = {
  appId: 'com.brain.trainer',
  appName: '脑力训练',
  webDir: 'www',
};

export default config;
```

- [ ] **Step 4: 创建 www 目录和占位文件**

确保 `www/` 和 `www/shared/` 目录存在：

```powershell
New-Item -ItemType Directory -Path "www" -Force
New-Item -ItemType Directory -Path "www\shared" -Force
```

- [ ] **Step 5: 提交**

```powershell
git add package.json package-lock.json capacitor.config.ts node_modules/ .gitignore
git commit -m "chore: init Capacitor project for brain-trainer"
```

---

### Task 2: 创建共享返回按钮脚本

**Files:**
- Create: `www/shared/back-btn.js`
- Create: `www/shared/style.css`

- [ ] **Step 1: 编写 back-btn.js**

文件: `www/shared/back-btn.js`

```javascript
(function () {
  // 获取脚本标签上的 data-position 属性，默认 'top-left'
  var currentScript = document.currentScript;
  var position = (currentScript && currentScript.getAttribute('data-position')) || 'top-left';

  // 检测是否已存在返回按钮，避免重复注入
  if (document.getElementById('__back_btn')) return;

  var btn = document.createElement('button');
  btn.id = '__back_btn';
  btn.innerHTML = '&#8592;';
  btn.onclick = function () {
    location.href = 'index.html';
  };

  // 固定位置样式
  var baseStyle =
    'position:fixed;z-index:9999;width:44px;height:44px;' +
    'border:none;border-radius:50%;background:rgba(255,255,255,0.85);' +
    'color:#333;font-size:22px;line-height:44px;text-align:center;' +
    'cursor:pointer;box-shadow:0 2px 8px rgba(0,0,0,0.15);' +
    'backdrop-filter:blur(10px);-webkit-backdrop-filter:blur(10px);' +
    'padding:0;margin:0;';

  if (position === 'top-right') {
    btn.style.cssText = baseStyle + 'top:16px;right:16px;';
  } else {
    btn.style.cssText = baseStyle + 'top:16px;left:16px;';
  }

  document.body.appendChild(btn);
})();
```

- [ ] **Step 2: 编写 shared/style.css**

文件: `www/shared/style.css`

```css
/* 返回按钮 - 防止被游戏内样式覆盖 */
#__back_btn {
  position: fixed !important;
  z-index: 9999 !important;
  width: 44px !important;
  height: 44px !important;
  border: none !important;
  border-radius: 50% !important;
  background: rgba(255,255,255,0.85) !important;
  color: #333 !important;
  font-size: 22px !important;
  cursor: pointer !important;
  box-shadow: 0 2px 8px rgba(0,0,0,0.15) !important;
  backdrop-filter: blur(10px) !important;
  -webkit-backdrop-filter: blur(10px) !important;
  padding: 0 !important;
  margin: 0 !important;
  line-height: 44px !important;
  text-align: center !important;
  font-family: system-ui, sans-serif !important;
  box-sizing: border-box !important;
  min-width: unset !important;
  max-width: unset !important;
}

#__back_btn[data-pos="top-right"] {
  top: 16px !important;
  right: 16px !important;
  left: auto !important;
}

#__back_btn[data-pos="top-left"] {
  top: 16px !important;
  left: 16px !important;
}

/* 列表页基础样式 */
body.game-list-page {
  background: #f5f5f5;
  margin: 0;
  padding: 16px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  color: #333;
}

.game-list-header {
  font-size: 20px;
  font-weight: 700;
  margin-bottom: 16px;
}

.game-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.game-list-item {
  display: flex;
  align-items: center;
  background: #fff;
  border-radius: 10px;
  padding: 14px 16px;
  margin-bottom: 8px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
  text-decoration: none;
  color: #333;
  font-size: 16px;
  cursor: pointer;
}

.game-list-item .game-icon {
  width: 36px;
  height: 36px;
  border-radius: 8px;
  margin-right: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
  flex-shrink: 0;
}

.game-list-item .game-name {
  flex: 1;
}

.game-list-item .game-arrow {
  color: #ccc;
  font-size: 18px;
}
```

- [ ] **Step 3: 提交**

```powershell
git add www/shared/
git commit -m "feat: add shared back button and styles"
```

---

### Task 3: 创建游戏列表页

**Files:**
- Create: `www/index.html`

- [ ] **Step 1: 编写列表页**

文件: `www/index.html`

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>脑力训练</title>
  <link rel="stylesheet" href="shared/style.css">
</head>
<body class="game-list-page">
  <div class="game-list-header">脑力训练</div>
  <div class="game-list">
    <a class="game-list-item" href="sudoku.html">
      <div class="game-icon" style="background:#e8f5e9;color:#4caf50;">▦</div>
      <span class="game-name">数独</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="minesweeper.html">
      <div class="game-icon" style="background:#fff3e0;color:#ff9800;">⌷</div>
      <span class="game-name">扫雷</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="gomoku.html">
      <div class="game-icon" style="background:#fce4ec;color:#e91e63;">◎</div>
      <span class="game-name">五子棋</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="typing-test.html">
      <div class="game-icon" style="background:#e3f2fd;color:#2196f3;">⌨</div>
      <span class="game-name">打字测试</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="reaction-test.html">
      <div class="game-icon" style="background:#f3e5f5;color:#9c27b0;">⚡</div>
      <span class="game-name">反应测试</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="schulte-grid.html">
      <div class="game-icon" style="background:#e0f7fa;color:#0097a7;">⊞</div>
      <span class="game-name">舒尔特方格</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="schulte-pro.html">
      <div class="game-icon" style="background:#e8eaf6;color:#3f51b5;">⊞</div>
      <span class="game-name">舒尔特 Pro</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="number-memory.html">
      <div class="game-icon" style="background:#fce4ec;color:#c62828;">#</div>
      <span class="game-name">数字记忆</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="memory.html">
      <div class="game-icon" style="background:#f1f8e9;color:#689f38;">♠</div>
      <span class="game-name">记忆翻牌</span>
      <span class="game-arrow">›</span>
    </a>
    <a class="game-list-item" href="cps-test.html">
      <div class="game-icon" style="background:#fff8e1;color:#f9a825;">👆</div>
      <span class="game-name">CPS 测试</span>
      <span class="game-arrow">›</span>
    </a>
  </div>
</body>
</html>
```

- [ ] **Step 2: 提交**

```powershell
git add www/index.html
git commit -m "feat: add game list page"
```

---

### Task 4: 复制游戏文件到 www 并注入返回按钮

**Files:**
- Copy + Modify: `sudoku.html` → `www/sudoku.html`
- Copy + Modify: `minesweeper.html` → `www/minesweeper.html`
- Copy + Modify: `gomoku.html` → `www/gomoku.html`
- Copy + Modify: `typing-test.html` → `www/typing-test.html`
- Copy + Modify: `reaction-test.html` → `www/reaction-test.html`
- Copy + Modify: `schulte-grid.html` → `www/schulte-grid.html`
- Copy + Modify: `schulte-pro.html` → `www/schulte-pro.html`
- Copy + Modify: `number-memory.html` → `www/number-memory.html`
- Copy + Modify: `memory.html` → `www/memory.html`
- Copy + Modify: `cps-test.html` → `www/cps-test.html`

**注入策略:**

所有文件在 `</body>` 之前插入一行脚本标签。默认 `data-position="top-left"`。
以下文件因已有固定按钮需调整位置：

- `reaction-test.html`: 左上角已有主题按钮 → `data-position="top-right"`
- `number-memory.html`: 右上角已有静音按钮 → `data-position="top-left"` (默认)
- `memory.html`: 右上角已有静音按钮 → `data-position="top-left"` (默认)

- [ ] **Step 1: 复制 sudoku.html 并注入**

先复制文件，再在 `</body>` 前插入：

插入位置：`</body>` 之前的那一行。原文模式为 `</script>\n</body>`，在 `</body>` 之前插入：

```html
<script src="shared/back-btn.js"></script>
</body>
```

实际操作用 PowerShell 批量处理。每个文件在 `</body>` 前插入 `<script src="shared/back-btn.js"></script>`。

PowerShell 脚本：

```powershell
$games = @(
  @{src="sudoku.html";       pos="top-left"},
  @{src="minesweeper.html";  pos="top-left"},
  @{src="gomoku.html";       pos="top-left"},
  @{src="typing-test.html";  pos="top-left"},
  @{src="reaction-test.html";pos="top-right"},
  @{src="schulte-grid.html"; pos="top-left"},
  @{src="schulte-pro.html";  pos="top-left"},
  @{src="number-memory.html";pos="top-left"},
  @{src="memory.html";       pos="top-left"},
  @{src="cps-test.html";     pos="top-left"}
)

$srcDir = "C:\Users\YDN\Desktop\Projects for Open"
$dstDir = "C:\Users\YDN\Desktop\Projects for Open\brain-trainer\www"

foreach ($g in $games) {
  $srcPath = Join-Path $srcDir $g.src
  $dstPath = Join-Path $dstDir $g.src
  $content = Get-Content $srcPath -Raw
  $inject = "<script src=`"shared/back-btn.js`" data-position=`"$($g.pos)`"></script>`n</body>"
  $content = $content -replace '</body>', $inject
  Set-Content -Path $dstPath -Value $content -NoNewline
  Write-Output "Processed: $($g.src) -> $dstPath"
}
```

- [ ] **Step 2: 验证注入结果**

随机抽查一个文件确认 `shared/back-btn.js` 标签存在：

```powershell
Select-String -Path "www\sudoku.html" -Pattern "back-btn.js"
```

Expected: 找到匹配行

- [ ] **Step 3: 提交**

```powershell
git add www/*.html
git commit -m "feat: copy game files and inject back button scripts"
```

---

### Task 5: 配置 Capacitor Android 平台

**Files:**
- Create: `android/` (Capacitor 生成)
- Modify: `capacitor.config.ts`

- [ ] **Step 1: 安装 Android 平台**

```powershell
npm install @capacitor/android
npx cap add android
```

Expected: 生成 `android/` 目录

- [ ] **Step 2: 同步 web 资源到 Android**

```powershell
npx cap sync
```

Expected: `android/app/src/main/assets/public/` 下有游戏文件

- [ ] **Step 3: 配置 APK 信息 (可选)**

如果需要修改应用名称、图标等，此时编辑 `android/` 下的资源文件。不修改则跳过。

- [ ] **Step 4: 提交**

```powershell
git add android/ package.json package-lock.json
git commit -m "feat: add Android platform via Capacitor"
```

---

### Task 6: 构建测试 APK

- [ ] **Step 1: 检查 Android Studio 是否安装**

```powershell
if (Test-Path "C:\Program Files\Android\Android Studio") {
  Write-Output "Android Studio found"
} else {
  Write-Output "Android Studio NOT found - will use CLI method"
}
```

- [ ] **Step 2: 构建 APK**

方式 A - 有 Android Studio：

```powershell
npx cap open android
```

Android Studio 打开后: Build → Build Bundle(s) / APK(s) → Build APK(s)

方式 B - 仅命令行（需配置 ANDROID_HOME）：

```powershell
Set-Location -LiteralPath "android"
.\gradlew assembleDebug
```

APK 生成路径: `android/app/build/outputs/apk/debug/app-debug.apk`

- [ ] **Step 3: 安装到手机测试**

将 APK 传到手机并安装，验证：
- 首页游戏列表正常显示
- 点击进入游戏功能正常
- 返回按钮可回到列表
- 离线可以玩 (关闭网络)

---

## 附: 不需要做的事

- 不修改任何游戏的 JS 逻辑
- 不修改任何游戏的 CSS (除了注入带来的样式)
- 不添加数据持久化
- 不修改项目根目录下原有的 HTML 文件 (只复制, 不改原文件)
- 原 `index.html` 不动, 新的 `www/index.html` 是独立文件
