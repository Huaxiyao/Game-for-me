# 番茄钟 + 日记 + 待办 Android 应用设计文档

## 概述

一款个人使用的 Android 原生应用，集成番茄钟、日记、待办三个核心功能，采用底部导航栏切换。

## 技术栈

- **语言**：Kotlin
- **UI 框架**：Jetpack Compose
- **导航**：Navigation Compose
- **本地存储**：Room 数据库
- **架构**：单 Activity + MVVM
- **最低 SDK**：API 26 (Android 8.0)

## 项目结构

```
com.example.app/
├── MainActivity.kt
├── navigation/
│   ├── NavGraph.kt
│   └── Screen.kt
├── screens/
│   ├── pomodoro/
│   │   ├── PomodoroScreen.kt
│   │   └── PomodoroViewModel.kt
│   ├── diary/
│   │   ├── DiaryScreen.kt
│   │   ├── DiaryEditScreen.kt
│   │   └── DiaryViewModel.kt
│   └── todo/
│       ├── TodoScreen.kt
│       └── TodoViewModel.kt
├── data/
│   ├── database/
│   │   ├── AppDatabase.kt
│   │   └── dao/
│   │       ├── DiaryDao.kt
│   │       └── TodoDao.kt
│   └── model/
│       ├── DiaryEntry.kt
│       └── TodoItem.kt
└── ui/
    └── theme/
        ├── Theme.kt
        ├── Color.kt
        └── Type.kt
```

## 功能设计

### 1. 底部导航栏

三个 Tab：
- **番茄钟**（图标：计时器）
- **日记**（图标：书本/笔记）
- **待办**（图标：勾选框）

### 2. 番茄钟（基础版）

**界面元素**：
- 大号倒计时显示（分:秒）
- 圆形进度指示器
- 开始/暂停按钮
- 重置按钮
- 工作/休息状态标签

**逻辑**：
- 工作时长：25 分钟
- 休息时长：5 分钟
- 工作结束后自动切换到休息状态
- 休息结束后等待用户手动开始下一轮
- 倒计时期间不熄屏（使用 KeepScreenOn）

### 3. 日记

**列表页**：
- 按日期倒序排列
- 每条显示：日期、标题、内容预览（前 50 字）
- 右下角 FAB 按钮新建日记

**编辑页**：
- 标题输入框
- 富文本编辑区域（支持基本格式：加粗、列表）
- 图片插入功能（从相册选择或拍照）
- 保存/删除按钮

**数据模型**：
- id: Long (主键)
- title: String
- content: String (富文本存储为 HTML 或 Markdown)
- imageUris: String (逗号分隔的图片 URI)
- createdAt: Long (创建时间戳)
- updatedAt: Long (更新时间戳)

### 4. 待办（基础版）

**界面元素**：
- 待办列表（未完成在上，已完成在下）
- 每条显示：勾选框 + 待办文本
- 底部输入框 + 添加按钮
- 左滑删除

**数据模型**：
- id: Long (主键)
- content: String
- isCompleted: Boolean
- createdAt: Long

## 数据库设计

### Room 数据库

**AppDatabase**：
- version: 1
- entities: DiaryEntry, TodoItem

**DiaryDao**：
- getAll(): Flow<List<DiaryEntry>>
- getById(id: Long): Flow<DiaryEntry>
- insert(entry: DiaryEntry)
- update(entry: DiaryEntry)
- delete(entry: DiaryEntry)

**TodoDao**：
- getAll(): Flow<List<TodoItem>>
- insert(item: TodoItem)
- update(item: TodoItem)
- delete(item: TodoItem)

## 多语言支持

- 支持中文和英文两种语言
- 使用 Android 字符串资源系统（strings.xml）
- 默认语言跟随系统设置
- 在设置页面提供语言切换选项（中文/英文/跟随系统）
- 资源文件：
  - `res/values/strings.xml`（英文 - 默认）
  - `res/values-zh/strings.xml`（中文）

## UI 风格

- Material 3 设计语言
- 浅色/深色主题跟随系统
- 主色调：绿色系（代表专注与成长）
- 圆角卡片布局
- 简洁清爽，无多余装饰
