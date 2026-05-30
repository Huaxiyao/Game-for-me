# 番茄钟 + 日记 + 待办 Android 应用实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 创建一个集成番茄钟、日记、待办三个功能的 Android 原生应用

**Architecture:** 单 Activity + Jetpack Compose + Navigation Compose + Room 数据库 + MVVM 架构

**Tech Stack:** Kotlin, Jetpack Compose, Navigation Compose, Room, Material 3

---

## 项目初始化

### Task 1: 创建 Android 项目基础结构

**Files:**
- Create: `app/build.gradle.kts`
- Create: `build.gradle.kts`
- Create: `settings.gradle.kts`
- Create: `gradle.properties`
- Create: `app/src/main/AndroidManifest.xml`

- [ ] **Step 1: 创建项目根目录结构**

```bash
mkdir -p PomodoroDiaryTodo/app/src/main/java/com/example/pomodorodiarytodo
mkdir -p PomodoroDiaryTodo/app/src/main/res/values
mkdir -p PomodoroDiaryTodo/app/src/main/res/values-zh
mkdir -p PomodoroDiaryTodo/gradle/wrapper
```

- [ ] **Step 2: 创建 settings.gradle.kts**

```kotlin
// settings.gradle.kts
pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}
dependencyResolution {
    @Suppress("UnstableApiUsage")
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "PomodoroDiaryTodo"
include(":app")
```

- [ ] **Step 3: 创建根 build.gradle.kts**

```kotlin
// build.gradle.kts
plugins {
    id("com.android.application") version "8.2.0" apply false
    id("org.jetbrains.kotlin.android") version "1.9.20" apply false
    id("com.google.devtools.ksp") version "1.9.20-1.0.14" apply false
}
```

- [ ] **Step 4: 创建 app/build.gradle.kts**

```kotlin
// app/build.gradle.kts
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
    id("com.google.devtools.ksp")
}

android {
    namespace = "com.example.pomodorodiarytodo"
    compileSdk = 34

    defaultConfig {
        applicationId = "com.example.pomodorodiarytodo"
        minSdk = 26
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
        vectorDrawables {
            useSupportLibrary = true
        }
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = "1.8"
    }
    buildFeatures {
        compose = true
    }
    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.5"
    }
    packaging {
        resources {
            excludes += "/META-INF/{AL2.0,LGPL2.1}"
        }
    }
}

dependencies {
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.6.2")
    implementation("androidx.activity:activity-compose:1.8.1")
    implementation("androidx.compose.ui:ui:1.5.4")
    implementation("androidx.compose.ui:ui-graphics:1.5.4")
    implementation("androidx.compose.ui:ui-tooling-preview:1.5.4")
    implementation("androidx.compose.material3:material3:1.1.2")
    implementation("androidx.compose.material:material-icons-extended:1.5.4")
    
    // Navigation
    implementation("androidx.navigation:navigation-compose:2.7.5")
    
    // Room
    implementation("androidx.room:room-runtime:2.6.1")
    implementation("androidx.room:room-ktx:2.6.1")
    ksp("androidx.room:room-compiler:2.6.1")
    
    // ViewModel
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.6.2")
    implementation("androidx.lifecycle:lifecycle-runtime-compose:2.6.2")
    
    // Testing
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
    androidTestImplementation("androidx.compose.ui:ui-test-junit4:1.5.4")
    debugImplementation("androidx.compose.ui:ui-tooling:1.5.4")
    debugImplementation("androidx.compose.ui:ui-test-manifest:1.5.4")
}
```

- [ ] **Step 5: 创建 gradle.properties**

```properties
# gradle.properties
org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8
android.useAndroidX=true
kotlin.code.style=official
android.nonTransitiveRClass=true
```

- [ ] **Step 6: 创建 AndroidManifest.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.PomodoroDiaryTodo"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:theme="@style/Theme.PomodoroDiaryTodo">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

- [ ] **Step 7: 提交**

```bash
git add .
git commit -m "feat: 初始化 Android 项目结构"
```

---

## 数据层实现

### Task 2: 创建数据模型和数据库

**Files:**
- Create: `app/src/main/java/com/example/pomodorodiarytodo/data/model/DiaryEntry.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/data/model/TodoItem.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/data/database/dao/DiaryDao.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/data/database/dao/TodoDao.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/data/database/AppDatabase.kt`

- [ ] **Step 1: 创建 DiaryEntry 数据模型**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/data/model/DiaryEntry.kt
package com.example.pomodorodiarytodo.data.model

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "diary_entries")
data class DiaryEntry(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val title: String,
    val content: String,
    val imageUris: String = "",
    val createdAt: Long = System.currentTimeMillis(),
    val updatedAt: Long = System.currentTimeMillis()
)
```

- [ ] **Step 2: 创建 TodoItem 数据模型**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/data/model/TodoItem.kt
package com.example.pomodorodiarytodo.data.model

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "todo_items")
data class TodoItem(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val content: String,
    val isCompleted: Boolean = false,
    val createdAt: Long = System.currentTimeMillis()
)
```

- [ ] **Step 3: 创建 DiaryDao**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/data/database/dao/DiaryDao.kt
package com.example.pomodorodiarytodo.data.database.dao

import androidx.room.*
import com.example.pomodorodiarytodo.data.model.DiaryEntry
import kotlinx.coroutines.flow.Flow

@Dao
interface DiaryDao {
    @Query("SELECT * FROM diary_entries ORDER BY createdAt DESC")
    fun getAll(): Flow<List<DiaryEntry>>

    @Query("SELECT * FROM diary_entries WHERE id = :id")
    fun getById(id: Long): Flow<DiaryEntry?>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(entry: DiaryEntry): Long

    @Update
    suspend fun update(entry: DiaryEntry)

    @Delete
    suspend fun delete(entry: DiaryEntry)
}
```

- [ ] **Step 4: 创建 TodoDao**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/data/database/dao/TodoDao.kt
package com.example.pomodorodiarytodo.data.database.dao

import androidx.room.*
import com.example.pomodorodiarytodo.data.model.TodoItem
import kotlinx.coroutines.flow.Flow

@Dao
interface TodoDao {
    @Query("SELECT * FROM todo_items ORDER BY isCompleted ASC, createdAt DESC")
    fun getAll(): Flow<List<TodoItem>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(item: TodoItem): Long

    @Update
    suspend fun update(item: TodoItem)

    @Delete
    suspend fun delete(item: TodoItem)
}
```

- [ ] **Step 5: 创建 AppDatabase**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/data/database/AppDatabase.kt
package com.example.pomodorodiarytodo.data.database

import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase
import com.example.pomodorodiarytodo.data.database.dao.DiaryDao
import com.example.pomodorodiarytodo.data.database.dao.TodoDao
import com.example.pomodorodiarytodo.data.model.DiaryEntry
import com.example.pomodorodiarytodo.data.model.TodoItem

@Database(
    entities = [DiaryEntry::class, TodoItem::class],
    version = 1,
    exportSchema = false
)
abstract class AppDatabase : RoomDatabase() {
    abstract fun diaryDao(): DiaryDao
    abstract fun todoDao(): TodoDao

    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null

        fun getDatabase(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "pomodoro_diary_todo_database"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

- [ ] **Step 6: 提交**

```bash
git add .
git commit -m "feat: 添加数据模型和 Room 数据库"
```

---

## 主题和资源

### Task 3: 创建主题、颜色和字符串资源

**Files:**
- Create: `app/src/main/java/com/example/pomodorodiarytodo/ui/theme/Color.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/ui/theme/Theme.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/ui/theme/Type.kt`
- Create: `app/src/main/res/values/strings.xml`
- Create: `app/src/main/res/values-zh/strings.xml`
- Create: `app/src/main/res/values/themes.xml`

- [ ] **Step 1: 创建 Color.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/ui/theme/Color.kt
package com.example.pomodorodiarytodo.ui.theme

import androidx.compose.ui.graphics.Color

val Green80 = Color(0xFFA5D6A7)
val GreenGrey80 = Color(0xFFC8E6C9)
val Teal80 = Color(0xFF80CBC4)

val Green40 = Color(0xFF4CAF50)
val GreenGrey40 = Color(0xFF81C784)
val Teal40 = Color(0xFF26A69A)

val Red500 = Color(0xFFF44336)
val Orange500 = Color(0xFFFF9800)
```

- [ ] **Step 2: 创建 Theme.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/ui/theme/Theme.kt
package com.example.pomodorodiarytodo.ui.theme

import android.app.Activity
import android.os.Build
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.dynamicDarkColorScheme
import androidx.compose.material3.dynamicLightColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable
import androidx.compose.runtime.SideEffect
import androidx.compose.ui.graphics.toArgb
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.platform.LocalView
import androidx.core.view.WindowCompat

private val DarkColorScheme = darkColorScheme(
    primary = Green80,
    secondary = GreenGrey80,
    tertiary = Teal80
)

private val LightColorScheme = lightColorScheme(
    primary = Green40,
    secondary = GreenGrey40,
    tertiary = Teal40
)

@Composable
fun PomodoroDiaryTodoTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }
        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }
    val view = LocalView.current
    if (!view.isInEditMode) {
        SideEffect {
            val window = (view.context as Activity).window
            window.statusBarColor = colorScheme.primary.toArgb()
            WindowCompat.getInsetsController(window, view).isAppearanceLightStatusBars = !darkTheme
        }
    }

    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        content = content
    )
}
```

- [ ] **Step 3: 创建 Type.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/ui/theme/Type.kt
package com.example.pomodorodiarytodo.ui.theme

import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Typography = Typography(
    bodyLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 16.sp,
        lineHeight = 24.sp,
        letterSpacing = 0.5.sp
    ),
    titleLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 22.sp,
        lineHeight = 28.sp,
        letterSpacing = 0.sp
    ),
    labelSmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Medium,
        fontSize = 11.sp,
        lineHeight = 16.sp,
        letterSpacing = 0.5.sp
    )
)
```

- [ ] **Step 4: 创建英文 strings.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">Pomodoro Diary Todo</string>
    
    <!-- Navigation -->
    <string name="nav_pomodoro">Pomodoro</string>
    <string name="nav_diary">Diary</string>
    <string name="nav_todo">Todo</string>
    
    <!-- Pomodoro -->
    <string name="pomodoro_work">Work</string>
    <string name="pomodoro_rest">Rest</string>
    <string name="pomodoro_start">Start</string>
    <string name="pomodoro_pause">Pause</string>
    <string name="pomodoro_reset">Reset</string>
    
    <!-- Diary -->
    <string name="diary_new">New Diary</string>
    <string name="diary_title">Title</string>
    <string name="diary_content">Content</string>
    <string name="diary_save">Save</string>
    <string name="diary_delete">Delete</string>
    <string name="diary_empty">No diary entries yet</string>
    
    <!-- Todo -->
    <string name="todo_add">Add Todo</string>
    <string name="todo_input">Enter todo content</string>
    <string name="todo_empty">No todo items yet</string>
    
    <!-- Settings -->
    <string name="settings_language">Language</string>
    <string name="settings_language_system">Follow System</string>
    <string name="settings_language_en">English</string>
    <string name="settings_language_zh">Chinese</string>
</resources>
```

- [ ] **Step 5: 创建中文 strings.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">番茄钟日记待办</string>
    
    <!-- Navigation -->
    <string name="nav_pomodoro">番茄钟</string>
    <string name="nav_diary">日记</string>
    <string name="nav_todo">待办</string>
    
    <!-- Pomodoro -->
    <string name="pomodoro_work">工作</string>
    <string name="pomodoro_rest">休息</string>
    <string name="pomodoro_start">开始</string>
    <string name="pomodoro_pause">暂停</string>
    <string name="pomodoro_reset">重置</string>
    
    <!-- Diary -->
    <string name="diary_new">新建日记</string>
    <string name="diary_title">标题</string>
    <string name="diary_content">内容</string>
    <string name="diary_save">保存</string>
    <string name="diary_delete">删除</string>
    <string name="diary_empty">还没有日记</string>
    
    <!-- Todo -->
    <string name="todo_add">添加待办</string>
    <string name="todo_input">输入待办内容</string>
    <string name="todo_empty">还没有待办事项</string>
    
    <!-- Settings -->
    <string name="settings_language">语言</string>
    <string name="settings_language_system">跟随系统</string>
    <string name="settings_language_en">英文</string>
    <string name="settings_language_zh">中文</string>
</resources>
```

- [ ] **Step 6: 创建 themes.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="Theme.PomodoroDiaryTodo" parent="android:Theme.Material.Light.NoActionBar" />
</resources>
```

- [ ] **Step 7: 提交**

```bash
git add .
git commit -m "feat: 添加主题、颜色和多语言字符串资源"
```

---

## 导航和主界面

### Task 4: 创建导航和主界面

**Files:**
- Create: `app/src/main/java/com/example/pomodorodiarytodo/navigation/Screen.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/navigation/NavGraph.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/MainActivity.kt`

- [ ] **Step 1: 创建 Screen.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/navigation/Screen.kt
package com.example.pomodorodiarytodo.navigation

import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Timer
import androidx.compose.material.icons.filled.Book
import androidx.compose.material.icons.filled.CheckCircle
import androidx.compose.ui.graphics.vector.ImageVector

sealed class Screen(
    val route: String,
    val title: String,
    val icon: ImageVector
) {
    object Pomodoro : Screen(
        route = "pomodoro",
        title = "番茄钟",
        icon = Icons.Default.Timer
    )

    object Diary : Screen(
        route = "diary",
        title = "日记",
        icon = Icons.Default.Book
    )

    object Todo : Screen(
        route = "todo",
        title = "待办",
        icon = Icons.Default.CheckCircle
    )

    object DiaryEdit : Screen(
        route = "diary_edit/{diaryId}",
        title = "编辑日记",
        icon = Icons.Default.Book
    ) {
        fun createRoute(diaryId: Long = -1) = "diary_edit/$diaryId"
    }
}
```

- [ ] **Step 2: 创建 NavGraph.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/navigation/NavGraph.kt
package com.example.pomodorodiarytodo.navigation

import androidx.compose.runtime.Composable
import androidx.navigation.NavHostController
import androidx.navigation.NavType
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.navArgument
import com.example.pomodorodiarytodo.screens.diary.DiaryEditScreen
import com.example.pomodorodiarytodo.screens.diary.DiaryScreen
import com.example.pomodorodiarytodo.screens.pomodoro.PomodoroScreen
import com.example.pomodorodiarytodo.screens.todo.TodoScreen

@Composable
fun NavGraph(navController: NavHostController) {
    NavHost(
        navController = navController,
        startDestination = Screen.Pomodoro.route
    ) {
        composable(Screen.Pomodoro.route) {
            PomodoroScreen()
        }

        composable(Screen.Diary.route) {
            DiaryScreen(
                onNavigateToEdit = { diaryId ->
                    navController.navigate(Screen.DiaryEdit.createRoute(diaryId))
                }
            )
        }

        composable(Screen.Todo.route) {
            TodoScreen()
        }

        composable(
            route = Screen.DiaryEdit.route,
            arguments = listOf(
                navArgument("diaryId") {
                    type = NavType.LongType
                    defaultValue = -1L
                }
            )
        ) { backStackEntry ->
            val diaryId = backStackEntry.arguments?.getLong("diaryId") ?: -1L
            DiaryEditScreen(
                diaryId = diaryId,
                onNavigateBack = {
                    navController.popBackStack()
                }
            )
        }
    }
}
```

- [ ] **Step 3: 创建 MainActivity.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/MainActivity.kt
package com.example.pomodorodiarytodo

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Settings
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.res.stringResource
import androidx.navigation.NavDestination.Companion.hierarchy
import androidx.navigation.NavGraph.Companion.findStartDestination
import androidx.navigation.compose.currentBackStackEntryAsState
import androidx.navigation.compose.rememberNavController
import com.example.pomodorodiarytodo.navigation.NavGraph
import com.example.pomodorodiarytodo.navigation.Screen
import com.example.pomodorodiarytodo.ui.theme.PomodoroDiaryTodoTheme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            PomodoroDiaryTodoTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    MainScreen()
                }
            }
        }
    }
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun MainScreen() {
    val navController = rememberNavController()
    val screens = listOf(
        Screen.Pomodoro,
        Screen.Diary,
        Screen.Todo
    )

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text(stringResource(R.string.app_name)) },
                actions = {
                    IconButton(onClick = { /* TODO: Settings */ }) {
                        Icon(Icons.Default.Settings, contentDescription = "Settings")
                    }
                }
            )
        },
        bottomBar = {
            NavigationBar {
                val navBackStackEntry by navController.currentBackStackEntryAsState()
                val currentDestination = navBackStackEntry?.destination
                
                screens.forEach { screen ->
                    NavigationBarItem(
                        icon = { Icon(screen.icon, contentDescription = screen.title) },
                        label = { Text(screen.title) },
                        selected = currentDestination?.hierarchy?.any { it.route == screen.route } == true,
                        onClick = {
                            navController.navigate(screen.route) {
                                popUpTo(navController.graph.findStartDestination().id) {
                                    saveState = true
                                }
                                launchSingleTop = true
                                restoreState = true
                            }
                        }
                    )
                }
            }
        }
    ) { innerPadding ->
        NavGraph(
            navController = navController
        )
    }
}
```

- [ ] **Step 4: 提交**

```bash
git add .
git commit -m "feat: 添加导航和主界面框架"
```

---

## 番茄钟功能

### Task 5: 实现番茄钟功能

**Files:**
- Create: `app/src/main/java/com/example/pomodorodiarytodo/screens/pomodoro/PomodoroViewModel.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/screens/pomodoro/PomodoroScreen.kt`

- [ ] **Step 1: 创建 PomodoroViewModel.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/screens/pomodoro/PomodoroViewModel.kt
package com.example.pomodorodiarytodo.screens.pomodoro

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.Job
import kotlinx.coroutines.delay
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.launch

class PomodoroViewModel : ViewModel() {
    private val workDuration = 25 * 60 * 1000L // 25 minutes
    private val restDuration = 5 * 60 * 1000L  // 5 minutes

    private val _timeLeft = MutableStateFlow(workDuration)
    val timeLeft: StateFlow<Long> = _timeLeft.asStateFlow()

    private val _isWorking = MutableStateFlow(true)
    val isWorking: StateFlow<Boolean> = _isWorking.asStateFlow()

    private val _isRunning = MutableStateFlow(false)
    val isRunning: StateFlow<Boolean> = _isRunning.asStateFlow()

    private var timerJob: Job? = null

    fun startTimer() {
        if (_isRunning.value) return
        
        _isRunning.value = true
        timerJob = viewModelScope.launch {
            while (_timeLeft.value > 0) {
                delay(1000)
                _timeLeft.value -= 1000
            }
            // Timer finished
            _isRunning.value = false
            if (_isWorking.value) {
                _isWorking.value = false
                _timeLeft.value = restDuration
            } else {
                _isWorking.value = true
                _timeLeft.value = workDuration
            }
        }
    }

    fun pauseTimer() {
        timerJob?.cancel()
        _isRunning.value = false
    }

    fun resetTimer() {
        timerJob?.cancel()
        _isRunning.value = false
        _isWorking.value = true
        _timeLeft.value = workDuration
    }

    fun formatTime(millis: Long): String {
        val minutes = millis / 1000 / 60
        val seconds = millis / 1000 % 60
        return "%02d:%02d".format(minutes, seconds)
    }

    fun getProgress(): Float {
        val totalTime = if (_isWorking.value) workDuration else restDuration
        return _timeLeft.value.toFloat() / totalTime.toFloat()
    }

    override fun onCleared() {
        super.onCleared()
        timerJob?.cancel()
    }
}
```

- [ ] **Step 2: 创建 PomodoroScreen.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/screens/pomodoro/PomodoroScreen.kt
package com.example.pomodorodiarytodo.screens.pomodoro

import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.res.stringResource
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import androidx.lifecycle.viewmodel.compose.viewModel
import com.example.pomodorodiarytodo.R

@Composable
fun PomodoroScreen(
    viewModel: PomodoroViewModel = viewModel()
) {
    val timeLeft by viewModel.timeLeft.collectAsState()
    val isWorking by viewModel.isWorking.collectAsState()
    val isRunning by viewModel.isRunning.collectAsState()

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        // Status label
        Text(
            text = if (isWorking) stringResource(R.string.pomodoro_work) else stringResource(R.string.pomodoro_rest),
            style = MaterialTheme.typography.titleLarge,
            color = MaterialTheme.colorScheme.primary
        )

        Spacer(modifier = Modifier.height(32.dp))

        // Timer display with progress
        Box(
            contentAlignment = Alignment.Center
        ) {
            CircularProgressIndicator(
                progress = viewModel.getProgress(),
                modifier = Modifier.size(200.dp),
                strokeWidth = 8.dp,
                trackColor = MaterialTheme.colorScheme.surfaceVariant
            )
            
            Text(
                text = viewModel.formatTime(timeLeft),
                fontSize = 48.sp,
                fontWeight = FontWeight.Bold
            )
        }

        Spacer(modifier = Modifier.height(48.dp))

        // Control buttons
        Row(
            horizontalArrangement = Arrangement.spacedBy(16.dp)
        ) {
            if (isRunning) {
                Button(
                    onClick = { viewModel.pauseTimer() },
                    colors = ButtonDefaults.buttonColors(
                        containerColor = MaterialTheme.colorScheme.secondary
                    )
                ) {
                    Text(stringResource(R.string.pomodoro_pause))
                }
            } else {
                Button(
                    onClick = { viewModel.startTimer() }
                ) {
                    Text(stringResource(R.string.pomodoro_start))
                }
            }

            OutlinedButton(
                onClick = { viewModel.resetTimer() }
            ) {
                Text(stringResource(R.string.pomodoro_reset))
            }
        }
    }
}
```

- [ ] **Step 3: 提交**

```bash
git add .
git commit -m "feat: 实现番茄钟功能"
```

---

## 待办功能

### Task 6: 实现待办功能

**Files:**
- Create: `app/src/main/java/com/example/pomodorodiarytodo/screens/todo/TodoViewModel.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/screens/todo/TodoScreen.kt`

- [ ] **Step 1: 创建 TodoViewModel.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/screens/todo/TodoViewModel.kt
package com.example.pomodorodiarytodo.screens.todo

import android.app.Application
import androidx.lifecycle.AndroidViewModel
import androidx.lifecycle.viewModelScope
import com.example.pomodorodiarytodo.data.database.AppDatabase
import com.example.pomodorodiarytodo.data.model.TodoItem
import kotlinx.coroutines.flow.*
import kotlinx.coroutines.launch

class TodoViewModel(application: Application) : AndroidViewModel(application) {
    private val database = AppDatabase.getDatabase(application)
    private val todoDao = database.todoDao()

    val todoItems: StateFlow<List<TodoItem>> = todoDao.getAll()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = emptyList()
        )

    fun addTodo(content: String) {
        if (content.isBlank()) return
        
        viewModelScope.launch {
            todoDao.insert(TodoItem(content = content))
        }
    }

    fun toggleTodo(item: TodoItem) {
        viewModelScope.launch {
            todoDao.update(item.copy(isCompleted = !item.isCompleted))
        }
    }

    fun deleteTodo(item: TodoItem) {
        viewModelScope.launch {
            todoDao.delete(item)
        }
    }
}
```

- [ ] **Step 2: 创建 TodoScreen.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/screens/todo/TodoScreen.kt
package com.example.pomodorodiarytodo.screens.todo

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Delete
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.res.stringResource
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.dp
import androidx.lifecycle.viewmodel.compose.viewModel
import com.example.pomodorodiarytodo.R
import com.example.pomodorodiarytodo.data.model.TodoItem

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun TodoScreen(
    viewModel: TodoViewModel = viewModel()
) {
    val todoItems by viewModel.todoItems.collectAsState()
    var newTodoText by remember { mutableStateOf("") }

    Column(
        modifier = Modifier.fillMaxSize()
    ) {
        // Input area
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            OutlinedTextField(
                value = newTodoText,
                onValueChange = { newTodoText = it },
                modifier = Modifier.weight(1f),
                placeholder = { Text(stringResource(R.string.todo_input)) },
                singleLine = true
            )
            
            Spacer(modifier = Modifier.width(8.dp))
            
            FloatingActionButton(
                onClick = {
                    viewModel.addTodo(newTodoText)
                    newTodoText = ""
                }
            ) {
                Icon(Icons.Default.Add, contentDescription = stringResource(R.string.todo_add))
            }
        }

        // Todo list
        if (todoItems.isEmpty()) {
            Box(
                modifier = Modifier.fillMaxSize(),
                contentAlignment = Alignment.Center
            ) {
                Text(
                    text = stringResource(R.string.todo_empty),
                    style = MaterialTheme.typography.bodyLarge,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
            }
        } else {
            LazyColumn(
                modifier = Modifier.fillMaxSize(),
                contentPadding = PaddingValues(16.dp),
                verticalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                items(todoItems) { item ->
                    TodoItemCard(
                        item = item,
                        onToggle = { viewModel.toggleTodo(item) },
                        onDelete = { viewModel.deleteTodo(item) }
                    )
                }
            }
        }
    }
}

@Composable
fun TodoItemCard(
    item: TodoItem,
    onToggle: () -> Unit,
    onDelete: () -> Unit
) {
    Card(
        modifier = Modifier.fillMaxWidth()
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            Checkbox(
                checked = item.isCompleted,
                onCheckedChange = { onToggle() }
            )
            
            Text(
                text = item.content,
                modifier = Modifier.weight(1f),
                style = MaterialTheme.typography.bodyLarge.copy(
                    textDecoration = if (item.isCompleted) TextDecoration.LineThrough else TextDecoration.None
                )
            )
            
            IconButton(onClick = onDelete) {
                Icon(
                    Icons.Default.Delete,
                    contentDescription = "Delete",
                    tint = MaterialTheme.colorScheme.error
                )
            }
        }
    }
}
```

- [ ] **Step 3: 提交**

```bash
git add .
git commit -m "feat: 实现待办功能"
```

---

## 日记功能

### Task 7: 实现日记功能

**Files:**
- Create: `app/src/main/java/com/example/pomodorodiarytodo/screens/diary/DiaryViewModel.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/screens/diary/DiaryScreen.kt`
- Create: `app/src/main/java/com/example/pomodorodiarytodo/screens/diary/DiaryEditScreen.kt`

- [ ] **Step 1: 创建 DiaryViewModel.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/screens/diary/DiaryViewModel.kt
package com.example.pomodorodiarytodo.screens.diary

import android.app.Application
import androidx.lifecycle.AndroidViewModel
import androidx.lifecycle.viewModelScope
import com.example.pomodorodiarytodo.data.database.AppDatabase
import com.example.pomodorodiarytodo.data.model.DiaryEntry
import kotlinx.coroutines.flow.*
import kotlinx.coroutines.launch

class DiaryViewModel(application: Application) : AndroidViewModel(application) {
    private val database = AppDatabase.getDatabase(application)
    private val diaryDao = database.diaryDao()

    val diaryEntries: StateFlow<List<DiaryEntry>> = diaryDao.getAll()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = emptyList()
        )

    private val _currentDiary = MutableStateFlow<DiaryEntry?>(null)
    val currentDiary: StateFlow<DiaryEntry?> = _currentDiary.asStateFlow()

    fun loadDiary(id: Long) {
        if (id == -1L) {
            _currentDiary.value = null
            return
        }
        
        viewModelScope.launch {
            diaryDao.getById(id).collect { entry ->
                _currentDiary.value = entry
            }
        }
    }

    fun saveDiary(title: String, content: String, imageUris: String = "") {
        val current = _currentDiary.value
        
        viewModelScope.launch {
            if (current != null) {
                diaryDao.update(
                    current.copy(
                        title = title,
                        content = content,
                        imageUris = imageUris,
                        updatedAt = System.currentTimeMillis()
                    )
                )
            } else {
                diaryDao.insert(
                    DiaryEntry(
                        title = title,
                        content = content,
                        imageUris = imageUris
                    )
                )
            }
        }
    }

    fun deleteDiary(entry: DiaryEntry) {
        viewModelScope.launch {
            diaryDao.delete(entry)
        }
    }
}
```

- [ ] **Step 2: 创建 DiaryScreen.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/screens/diary/DiaryScreen.kt
package com.example.pomodorodiarytodo.screens.diary

import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.res.stringResource
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import androidx.lifecycle.viewmodel.compose.viewModel
import com.example.pomodorodiarytodo.R
import com.example.pomodorodiarytodo.data.model.DiaryEntry
import java.text.SimpleDateFormat
import java.util.*

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun DiaryScreen(
    onNavigateToEdit: (Long) -> Unit,
    viewModel: DiaryViewModel = viewModel()
) {
    val diaryEntries by viewModel.diaryEntries.collectAsState()

    Scaffold(
        floatingActionButton = {
            FloatingActionButton(
                onClick = { onNavigateToEdit(-1) }
            ) {
                Icon(Icons.Default.Add, contentDescription = stringResource(R.string.diary_new))
            }
        }
    ) { innerPadding ->
        if (diaryEntries.isEmpty()) {
            Box(
                modifier = Modifier
                    .fillMaxSize()
                    .padding(innerPadding),
                contentAlignment = Alignment.Center
            ) {
                Text(
                    text = stringResource(R.string.diary_empty),
                    style = MaterialTheme.typography.bodyLarge,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
            }
        } else {
            LazyColumn(
                modifier = Modifier
                    .fillMaxSize()
                    .padding(innerPadding),
                contentPadding = PaddingValues(16.dp),
                verticalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                items(diaryEntries) { entry ->
                    DiaryEntryCard(
                        entry = entry,
                        onClick = { onNavigateToEdit(entry.id) }
                    )
                }
            }
        }
    }
}

@Composable
fun DiaryEntryCard(
    entry: DiaryEntry,
    onClick: () -> Unit
) {
    val dateFormat = SimpleDateFormat("yyyy-MM-dd HH:mm", Locale.getDefault())
    val dateStr = dateFormat.format(Date(entry.createdAt))

    Card(
        modifier = Modifier
            .fillMaxWidth()
            .clickable { onClick() }
    ) {
        Column(
            modifier = Modifier.padding(16.dp)
        ) {
            Text(
                text = entry.title,
                style = MaterialTheme.typography.titleMedium
            )
            
            Spacer(modifier = Modifier.height(4.dp))
            
            Text(
                text = dateStr,
                style = MaterialTheme.typography.bodySmall,
                color = MaterialTheme.colorScheme.onSurfaceVariant
            )
            
            Spacer(modifier = Modifier.height(8.dp))
            
            Text(
                text = entry.content.take(100),
                style = MaterialTheme.typography.bodyMedium,
                maxLines = 2,
                overflow = TextOverflow.Ellipsis
            )
        }
    }
}
```

- [ ] **Step 3: 创建 DiaryEditScreen.kt**

```kotlin
// app/src/main/java/com/example/pomodorodiarytodo/screens/diary/DiaryEditScreen.kt
package com.example.pomodorodiarytodo.screens.diary

import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowBack
import androidx.compose.material.icons.filled.Delete
import androidx.compose.material.icons.filled.Save
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.res.stringResource
import androidx.compose.ui.unit.dp
import androidx.lifecycle.viewmodel.compose.viewModel
import com.example.pomodorodiarytodo.R

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun DiaryEditScreen(
    diaryId: Long,
    onNavigateBack: () -> Unit,
    viewModel: DiaryViewModel = viewModel()
) {
    val currentDiary by viewModel.currentDiary.collectAsState()
    
    var title by remember { mutableStateOf("") }
    var content by remember { mutableStateOf("") }
    
    LaunchedEffect(diaryId) {
        viewModel.loadDiary(diaryId)
    }
    
    LaunchedEffect(currentDiary) {
        currentDiary?.let { diary ->
            title = diary.title
            content = diary.content
        }
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text(stringResource(R.string.diary_new)) },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Back")
                    }
                },
                actions = {
                    IconButton(
                        onClick = {
                            viewModel.saveDiary(title, content)
                            onNavigateBack()
                        }
                    ) {
                        Icon(Icons.Default.Save, contentDescription = stringResource(R.string.diary_save))
                    }
                    
                    if (currentDiary != null) {
                        IconButton(
                            onClick = {
                                viewModel.deleteDiary(currentDiary!!)
                                onNavigateBack()
                            }
                        ) {
                            Icon(
                                Icons.Default.Delete,
                                contentDescription = stringResource(R.string.diary_delete),
                                tint = MaterialTheme.colorScheme.error
                            )
                        }
                    }
                }
            )
        }
    ) { innerPadding ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(innerPadding)
                .padding(16.dp)
        ) {
            OutlinedTextField(
                value = title,
                onValueChange = { title = it },
                modifier = Modifier.fillMaxWidth(),
                placeholder = { Text(stringResource(R.string.diary_title)) },
                singleLine = true
            )
            
            Spacer(modifier = Modifier.height(16.dp))
            
            OutlinedTextField(
                value = content,
                onValueChange = { content = it },
                modifier = Modifier
                    .fillMaxWidth()
                    .weight(1f),
                placeholder = { Text(stringResource(R.string.diary_content)) }
            )
        }
    }
}
```

- [ ] **Step 4: 提交**

```bash
git add .
git commit -m "feat: 实现日记功能"
```

---

## 最终集成

### Task 8: 最终测试和打包

- [ ] **Step 1: 构建 APK**

```bash
cd PomodoroDiaryTodo
./gradlew assembleDebug
```

- [ ] **Step 2: 测试应用**

在模拟器或真机上安装并测试：
1. 番茄钟：启动、暂停、重置功能
2. 待办：添加、完成、删除功能
3. 日记：新建、编辑、删除功能
4. 底部导航切换
5. 中英文切换

- [ ] **Step 3: 最终提交**

```bash
git add .
git commit -m "feat: 完成所有功能实现"
```

---

## 完成

应用已准备就绪！APK 文件位于：
`app/build/outputs/apk/debug/app-debug.apk`
