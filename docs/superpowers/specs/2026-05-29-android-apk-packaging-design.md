# 脑力训练游戏 Android APK 打包设计

## 目标

将 9 个现有 HTML 游戏打包为单个 Android APK，离线使用，不上架应用商店。

## 现有资产

| 游戏 | 文件 |
|------|------|
| 数独 | sudoku.html |
| 扫雷 | minesweeper.html |
| 五子棋 | gomoku.html |
| 打字测试 | typing-test.html |
| 反应测试 | reaction-test.html |
| 舒尔特方格 | schulte-grid.html |
| 舒尔特专业版 | schulte-pro.html |
| 数字记忆 | number-memory.html |
| 记忆翻牌 | memory.html |
| CPS 测试 | cps-test.html |

## 技术选型

| 项目 | 选择 |
|------|------|
| 平台 | Android |
| 框架 | Capacitor |
| 分发 | APK 直装，不上商店 |
| 语言 | HTML/CSS/JS，无需写 Kotlin/Java |

## 项目结构

```
brain-trainer/
  www/
    index.html                 ← 游戏列表页（新写）
    shared/
      style.css                ← 共享样式（亮色卡片风格）
      back-btn.js              ← 返回按钮 + 共享逻辑
    sudoku.html                ← 仅底部加 loaded 脚本引入
    minesweeper.html           ← 同上
    gomoku.html                ← 同上
    typing-test.html           ← 同上
    reaction-test.html         ← 同上
    schulte-grid.html          ← 同上
    schulte-pro.html           ← 同上
    number-memory.html         ← 同上
    memory.html                ← 同上
    cps-test.html              ← 同上
  capacitor.config.ts          ← Capacitor 配置
  package.json                 ← 项目配置
```

## 导航设计

- **游戏列表页**：单列卡片列表，每项显示游戏名称，点击跳转到对应 HTML
- **游戏内**：顶部或底部固定返回箭头，点击回到 index.html
- 跳转方式：`location.href` 页面跳转

## 视觉设计

- 亮色背景 (#f5f5f5)，白色圆角卡片，微阴影
- 每个游戏卡片左 icon + 右箭头
- 返回按钮：固定位置，不遮挡游戏操作区
- 各游戏内部样式不变，仅注入共享返回按钮

## 游戏改动说明

每个现有 HTML 文件底部（`</body>` 之前）添加：

```html
<link rel="stylesheet" href="shared/style.css">
<script src="shared/back-btn.js"></script>
```

游戏本身的 JS 和 CSS 不做任何改动。

## 不做的事

- 不重构任何游戏逻辑
- 不加统计/设置/主题切换（可后续迭代）
- 不加数据持久化/最高分记录
- 不上架应用商店
