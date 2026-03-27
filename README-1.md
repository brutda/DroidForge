# 🔨 DroidForge IDE

> **The Android IDE built for Android. Code, build, and ship apps — entirely from your phone.**

---

## 📋 Project Overview

| Field | Value |
|---|---|
| **App Name** | DroidForge |
| **Package** | `com.droidforge.ide` |
| **Min SDK** | API 30 (Android 11) |
| **Target SDK** | API 35 (Android 15) |
| **Language** | Kotlin (100%) |
| **UI Toolkit** | Jetpack Compose (latest stable) |
| **Theme** | Dark-first Material You (Dynamic Color / Material3) |
| **Architecture** | Clean Architecture + MVI |
| **Build System** | Gradle (Kotlin DSL) |

---

## 🎯 Mission Statement

DroidForge is a **native Android IDE** that lets developers write Kotlin/Java, build APKs on-device, preview Jetpack Compose UI in real time, and manage Git repositories — all without ever touching a desktop. It is not a toy. It is not a code viewer. It is a **full development environment** engineered to feel as powerful and polished as any desktop IDE, with a touch-first UI designed from the ground up for phones and tablets.

---

## 🏗️ Tech Stack

### Core

| Layer | Technology |
|---|---|
| Language | Kotlin 2.x |
| UI | Jetpack Compose + Material3 |
| DI | Hilt |
| Async | Kotlin Coroutines + Flow |
| Navigation | Compose Navigation (type-safe) |
| Storage | Room (projects DB) + DataStore (prefs) |
| Networking | Ktor Client (AI, Git remote) |

### On-Device Build Engine

| Component | Technology |
|---|---|
| Kotlin Compiler | Kotlin Compiler Embeddable (`kotlin-compiler-embeddable`) |
| Java Compiler | ECJ (Eclipse Compiler for Java) — lightweight, no JDK needed |
| DEX Conversion | D8 (bundled as a library via `com.android.tools:r8`) |
| APK Packaging | `apksig` library + manual ZIP/alignment |
| Resource Processing | AAPT2 binary (bundled as an asset, extracted at runtime) |

### Code Editor

| Component | Technology |
|---|---|
| Editor Core | [Sora Editor](https://github.com/Rosemoe/sora-editor) — best-in-class Android code editor |
| Language Server | Custom LSP bridge (Kotlin LSP via Kotlin compiler analysis APIs) |
| Syntax Highlight | TextMate grammars via Sora |
| Autocomplete | Sora + custom symbol resolver + AI layer |

### AI Autocomplete

| Component | Technology |
|---|---|
| Provider | Anthropic Claude API (`claude-sonnet-4-20250514`) |
| Mode | Fill-in-the-middle (FIM) completions + chat assistant |
| Trigger | 600ms debounce after keystroke pause |
| Context | Current file + open tabs (trimmed to 4096 tokens) |
| Fallback | Offline symbol-based completion (no internet needed) |

### Git Integration

| Component | Technology |
|---|---|
| Library | JGit (pure-Java, works on Android) |
| Features | Init, clone (HTTPS/SSH), stage, commit, push, pull, branch, merge, diff viewer |
| Auth | SSH keypair (generated on-device) + HTTPS personal access tokens |

### Jetpack Compose Preview

| Component | Technology |
|---|---|
| Renderer | Custom Compose renderer in isolated process |
| Trigger | File save or manual refresh button |
| Display | SurfaceView with screenshot capture fallback |

---

## 📁 Project Structure

```
DroidForge/
├── app/
│   ├── src/main/
│   │   ├── AndroidManifest.xml
│   │   └── kotlin/com/droidforge/ide/
│   │       ├── DroidForgeApp.kt               # Application class, Hilt setup
│   │       ├── MainActivity.kt                # Single Activity host
│   │       │
│   │       ├── core/
│   │       │   ├── di/                        # Hilt modules
│   │       │   ├── theme/
│   │       │   │   ├── Theme.kt               # Material3 Dynamic Color setup
│   │       │   │   ├── Typography.kt          # JetBrains Mono + custom type scale
│   │       │   │   └── Color.kt               # Fallback palette (deep forge orange + dark)
│   │       │   ├── util/
│   │       │   │   ├── FileUtils.kt
│   │       │   │   ├── PermissionUtils.kt
│   │       │   │   └── ProcessUtils.kt        # Isolated process helpers
│   │       │   └── base/
│   │       │       ├── BaseViewModel.kt
│   │       │       └── UiState.kt
│   │       │
│   │       ├── navigation/
│   │       │   ├── DroidForgeNavHost.kt       # Root nav graph
│   │       │   └── Screen.kt                  # Sealed class of all screens
│   │       │
│   │       ├── feature/
│   │       │   │
│   │       │   ├── home/                      # Project launcher screen
│   │       │   │   ├── HomeScreen.kt
│   │       │   │   ├── HomeViewModel.kt
│   │       │   │   ├── ProjectCard.kt
│   │       │   │   └── NewProjectDialog.kt
│   │       │   │
│   │       │   ├── editor/                    # Main IDE screen
│   │       │   │   ├── EditorScreen.kt        # Root layout: file tree + editor + panels
│   │       │   │   ├── EditorViewModel.kt
│   │       │   │   ├── components/
│   │       │   │   │   ├── FileTreePanel.kt   # Collapsible file tree drawer
│   │       │   │   │   ├── TabBar.kt          # Open file tabs
│   │       │   │   │   ├── SoraEditorView.kt  # AndroidView wrapper for Sora Editor
│   │       │   │   │   ├── BottomPanel.kt     # Build output / terminal / problems
│   │       │   │   │   ├── ComposePreviewPanel.kt
│   │       │   │   │   └── SymbolBar.kt       # Touch keyboard extra symbols row
│   │       │   │   └── actions/
│   │       │   │       ├── EditorActions.kt   # Cut/copy/paste/find/replace
│   │       │   │       └── CodeActions.kt     # Format, comment, surround
│   │       │   │
│   │       │   ├── build/                     # On-device build engine
│   │       │   │   ├── BuildManager.kt        # Orchestrates full build pipeline
│   │       │   │   ├── pipeline/
│   │       │   │   │   ├── ResourceProcessor.kt   # Runs AAPT2
│   │       │   │   │   ├── KotlinCompiler.kt      # kotlin-compiler-embeddable
│   │       │   │   │   ├── JavaCompiler.kt        # ECJ wrapper
│   │       │   │   │   ├── DexConverter.kt        # D8 wrapper
│   │       │   │   │   └── ApkPackager.kt         # apksig + zipalign
│   │       │   │   ├── installer/
│   │       │   │   │   └── ApkInstaller.kt        # Intent-based or root install
│   │       │   │   ├── model/
│   │       │   │   │   ├── BuildConfig.kt
│   │       │   │   │   ├── BuildResult.kt
│   │       │   │   │   └── BuildLog.kt
│   │       │   │   └── BuildViewModel.kt
│   │       │   │
│   │       │   ├── git/                       # Git integration
│   │       │   │   ├── GitManager.kt          # JGit facade
│   │       │   │   ├── GitScreen.kt           # Commit/branch/log UI
│   │       │   │   ├── GitViewModel.kt
│   │       │   │   ├── components/
│   │       │   │   │   ├── CommitPanel.kt
│   │       │   │   │   ├── BranchList.kt
│   │       │   │   │   ├── DiffViewer.kt
│   │       │   │   │   └── RemoteDialog.kt
│   │       │   │   └── auth/
│   │       │   │       ├── SshKeyManager.kt
│   │       │   │       └── TokenStore.kt      # Encrypted with EncryptedSharedPreferences
│   │       │   │
│   │       │   ├── ai/                        # AI assistant & autocomplete
│   │       │   │   ├── AiManager.kt           # Claude API client (Ktor)
│   │       │   │   ├── AutocompleteProvider.kt
│   │       │   │   ├── AiChatScreen.kt        # Floating chat assistant panel
│   │       │   │   ├── AiViewModel.kt
│   │       │   │   └── model/
│   │       │   │       ├── CompletionRequest.kt
│   │       │   │       └── CompletionResponse.kt
│   │       │   │
│   │       │   ├── preview/                   # Compose live preview
│   │       │   │   ├── PreviewRenderer.kt     # Isolated process renderer
│   │       │   │   ├── PreviewService.kt      # Bound service in :preview process
│   │       │   │   └── PreviewViewModel.kt
│   │       │   │
│   │       │   ├── terminal/                  # Built-in terminal
│   │       │   │   ├── TerminalScreen.kt
│   │       │   │   ├── TerminalViewModel.kt
│   │       │   │   └── PtyManager.kt          # Uses libpty / ProcessBuilder
│   │       │   │
│   │       │   └── settings/
│   │       │       ├── SettingsScreen.kt
│   │       │       ├── SettingsViewModel.kt
│   │       │       └── EditorPreferences.kt   # Font size, tab size, theme overrides
│   │       │
│   │       └── data/
│   │           ├── db/
│   │           │   ├── DroidForgeDatabase.kt
│   │           │   ├── ProjectDao.kt
│   │           │   └── ProjectEntity.kt
│   │           ├── repository/
│   │           │   ├── ProjectRepository.kt
│   │           │   └── SettingsRepository.kt
│   │           └── model/
│   │               └── Project.kt
│   │
│   └── build.gradle.kts
│
├── build.gradle.kts
├── settings.gradle.kts
├── gradle/libs.versions.toml              # Version catalog
└── README.md
```

---

## 🎨 UI / UX Design Spec

### Theme System

- **Base**: Material3 `darkColorScheme` as default
- **Dynamic Color**: Enabled on Android 12+ via `DynamicColors.applyToActivityIfAvailable()`
- **Fallback palette** (Android 11):
  - Primary: `#FF6B2B` (Forge Orange)
  - Background: `#0D0D0D`
  - Surface: `#1A1A1A`
  - Surface Variant: `#242424`
  - On-Surface: `#E8E8E8`
- **Typography**: `JetBrains Mono` for all code, `Google Sans` (or `Nunito`) for UI labels
- **Shape**: Rounded corners throughout — `RoundedCornerShape(12.dp)` standard

### Screen Breakdown

#### 1. Home Screen (`HomeScreen.kt`)
- **Layout**: Full-screen lazy vertical grid of project cards (2 columns on phone, 3 on tablet)
- **Project Card**: Shows project name, language badge (Kotlin/Java), last modified timestamp, and a forge-flame icon per project
- **FAB**: `ExtendedFloatingActionButton` — "New Project" — anchored bottom-right
- **New Project Dialog**: Bottom sheet with fields: Name, Package, Language (Kotlin/Java toggle), Template (Empty, Basic Activity, Compose)
- **Top bar**: `DroidForge` logo wordmark left, search icon + settings icon right

#### 2. Editor Screen (`EditorScreen.kt`)
- **Layout**: `DrawerLayout`-style with:
  - **Left drawer**: File tree (collapsible, swipe to open)
  - **Center**: Tab bar + Sora editor (full height)
  - **Bottom sheet**: Draggable panel (Build Output / Terminal / Problems / Logcat tabs)
  - **Right FAB cluster**: Build ▶, Git branch icon, AI chat bubble
- **Symbol Bar**: Horizontal scrollable row above soft keyboard with: `{ } ( ) [ ] ; : . , -> = // /* */`
- **Tab Bar**: Horizontal scrollable, each tab shows file name + unsaved dot indicator + close button
- **Editor**: Sora editor with line numbers, bracket matching, code folding, find/replace toolbar

#### 3. Build Panel
- Slides up from bottom as a sheet when build is triggered
- Shows **real-time streaming log** in monospace text (colored: errors red, warnings yellow, success green)
- Progress bar during compile phases: Resources → Compile → DEX → Package → Install
- On success: green checkmark + "Install APK" button
- On failure: error count badge + tap-to-jump-to-line errors

#### 4. Git Screen (`GitScreen.kt`)
- Tab layout: **Changes | Branches | Log | Remotes**
- Changes: Two-pane (staged / unstaged) with swipe-to-stage, commit message field, commit button
- Diff Viewer: Side-by-side or unified diff with colored additions/deletions
- Branch list: Chips for local branches, create/delete/checkout actions

#### 5. AI Chat Panel
- Floating overlay panel (60% screen height bottom sheet)
- Chat bubble history with syntax-highlighted code blocks
- Input field with "Ask about this file", "Explain selection", "Fix error" quick actions
- Streams Claude responses token by token

#### 6. Settings Screen
- Editor: Font size slider, tab size, word wrap, line numbers toggle
- Build: JDK level selector, build variant (debug/release), signing config
- AI: API key input (masked), autocomplete toggle, completion delay slider
- Git: User name/email, SSH key viewer/regenerate, PAT management
- Appearance: Force light/dark/system, custom accent (Android 11 fallback)

---

## ⚙️ On-Device Build Pipeline — Deep Dive

Jules must implement this pipeline in `BuildManager.kt` as a sequential coroutine chain:

```
User taps Build
        │
        ▼
1. ResourceProcessor      (AAPT2 — compile resources → .flat files → link → R.java + resources.ap_)
        │
        ▼
2. KotlinCompiler         (kotlin-compiler-embeddable — src + R.java → .class files)
        │
        ▼
3. JavaCompiler           (ECJ — any .java files → .class files, merged with Kotlin output)
        │
        ▼
4. DexConverter           (D8 API — .class files → classes.dex)
        │
        ▼
5. ApkPackager            (Zip resources.ap_ + classes.dex + assets → unsigned.apk → sign with debug key → zipalign)
        │
        ▼
6. ApkInstaller           (ACTION_VIEW intent with FileProvider URI — triggers system installer)
```

**Important implementation notes for Jules:**

- AAPT2 binary must be bundled in `assets/tools/aapt2` and extracted to `filesDir` on first run, with `chmod +x`
- D8 must be invoked via its programmatic API (`com.android.tools.r8.D8`), not command line
- Debug keystore must be auto-generated on first run using `KeyStore` API and stored in `filesDir/debug.keystore`
- Build must run entirely in a background coroutine on `Dispatchers.IO`, emitting `BuildLog` events via `StateFlow` to the UI
- Each pipeline step emits start/progress/success/error log entries
- The entire `build/` directory lives inside the app's private storage — no external storage permission needed

---

## 🤖 AI Autocomplete — Implementation Spec

### Trigger Logic (`AutocompleteProvider.kt`)
```
User types character
    → Debounce 600ms
    → If cursor in middle of identifier: skip
    → Collect context: 60 lines before cursor + 20 lines after
    → Send to AiManager as FIM (fill-in-middle) request
    → Stream response
    → Display inline ghost text in Sora editor
    → Tab or → to accept, Esc to dismiss
```

### Claude API Request Format
```kotlin
// Prompt structure for FIM completion
val prompt = """
<fim_prefix>
${contextBefore}
</fim_prefix>
<fim_suffix>
${contextAfter}
</fim_suffix>
<fim_middle>
""".trimIndent()
```

### AI Chat Assistant
- Separate from autocomplete — user explicitly opens the panel
- System prompt: "You are DroidForge AI, an expert Android/Kotlin developer assistant embedded in the DroidForge IDE on Android. Help the user write, debug, and improve their Android app code. Be concise. Format code in Kotlin unless asked otherwise."
- Sends: current file content + user message + conversation history
- Renders: Markdown with code block syntax highlighting

---

## 🔀 Git Integration — Implementation Spec

All Git operations go through `GitManager.kt` which wraps JGit:

```kotlin
class GitManager(private val projectDir: File) {
    fun init(): Repository
    suspend fun clone(url: String, auth: GitAuth): Repository
    fun stage(files: List<String>)
    fun unstage(files: List<String>)
    suspend fun commit(message: String, author: String, email: String)
    suspend fun push(remote: String, branch: String, auth: GitAuth)
    suspend fun pull(remote: String, branch: String, auth: GitAuth)
    fun branches(): List<BranchInfo>
    fun createBranch(name: String)
    fun checkout(branch: String)
    fun diff(filePath: String): DiffResult
    fun log(maxCount: Int = 50): List<CommitInfo>
}
```

**SSH Auth**: Generate RSA-4096 keypair using `java.security.KeyPairGenerator`, store private key in `EncryptedFile` (Jetpack Security), expose public key to user for adding to GitHub/GitLab.

**HTTPS Auth**: Store PAT in `EncryptedSharedPreferences`. Pass as `UsernamePasswordCredentialsProvider("token", pat)` to JGit.

---

## 🖥️ Compose Live Preview — Implementation Spec

This is the hardest feature. Jules should implement a **simplified version** for v1:

**V1 approach (screenshot-based preview):**
1. User marks a file as having `@Preview` composables
2. On save, DroidForge triggers a fast incremental build (compile only the changed file + dependencies)
3. Runs the output in an isolated `android:process=":preview"` service
4. The service renders the composable to a `Bitmap` via `ComposeView` + `ViewTreeLifecycleOwner`
5. Returns the bitmap to the main process via AIDL
6. Main process displays it in `ComposePreviewPanel`

**V2 (future):** Live interactive preview via continuous rendering loop.

---

## 📦 Gradle Dependencies (`libs.versions.toml`)

```toml
[versions]
kotlin = "2.0.21"
compose-bom = "2024.12.01"
hilt = "2.52"
room = "2.6.1"
ktor = "3.0.3"
jgit = "7.1.0.202411261347-r"
sora-editor = "0.23.4"
kotlin-compiler-embeddable = "2.0.21"
r8 = "8.5.35"
apksig = "8.7.3"

[libraries]
# Compose
compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "compose-bom" }
compose-ui = { group = "androidx.compose.ui", name = "ui" }
compose-material3 = { group = "androidx.compose.material3", name = "material3" }
compose-preview = { group = "androidx.compose.ui", name = "ui-tooling-preview" }
activity-compose = { group = "androidx.activity", name = "activity-compose", version = "1.9.3" }

# Hilt
hilt-android = { group = "com.google.dagger", name = "hilt-android", version.ref = "hilt" }
hilt-compiler = { group = "com.google.dagger", name = "hilt-android-compiler", version.ref = "hilt" }

# Room
room-runtime = { group = "androidx.room", name = "room-runtime", version.ref = "room" }
room-ktx = { group = "androidx.room", name = "room-ktx", version.ref = "room" }
room-compiler = { group = "androidx.room", name = "room-compiler", version.ref = "room" }

# Ktor (AI + Git HTTPS)
ktor-client-android = { group = "io.ktor", name = "ktor-client-android", version.ref = "ktor" }
ktor-client-content-negotiation = { group = "io.ktor", name = "ktor-client-content-negotiation", version.ref = "ktor" }
ktor-serialization-json = { group = "io.ktor", name = "ktor-serialization-kotlinx-json", version.ref = "ktor" }

# JGit
jgit-core = { group = "org.eclipse.jgit", name = "org.eclipse.jgit", version.ref = "jgit" }
jgit-ssh = { group = "org.eclipse.jgit", name = "org.eclipse.jgit.ssh.apache", version.ref = "jgit" }

# Sora Editor
sora-editor = { group = "io.github.Rosemoe.sora-editor", name = "editor", version.ref = "sora-editor" }
sora-language-textmate = { group = "io.github.Rosemoe.sora-editor", name = "language-textmate", version.ref = "sora-editor" }

# Build tools
kotlin-compiler-embeddable = { group = "org.jetbrains.kotlin", name = "kotlin-compiler-embeddable", version.ref = "kotlin-compiler-embeddable" }
r8 = { group = "com.android.tools", name = "r8", version.ref = "r8" }
apksig = { group = "com.android.tools.build", name = "apksig", version.ref = "apksig" }

# Security
security-crypto = { group = "androidx.security", name = "security-crypto", version = "1.1.0-alpha06" }

# DataStore
datastore-preferences = { group = "androidx.datastore", name = "datastore-preferences", version = "1.1.1" }
```

---

## 🔐 Permissions (`AndroidManifest.xml`)

```xml
<!-- Required -->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />

<!-- For APK install via FileProvider -->
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"
    android:maxSdkVersion="32" />

<!-- Optional (terminal feature) -->
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
```

**FileProvider** must be declared for APK install on Android 11+:
```xml
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="com.droidforge.ide.fileprovider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/file_provider_paths" />
</provider>
```

---

## 🚀 Build & Run Instructions (for Jules)

### Step 1 — Clone & open
```bash
git clone https://github.com/your-org/droidforge.git
cd droidforge
# Open in Android Studio (desktop) for initial setup
```

### Step 2 — Configure AI key
Create `local.properties` and add:
```
ANTHROPIC_API_KEY=sk-ant-your-key-here
```
Reference it in `build.gradle.kts`:
```kotlin
buildConfigField("String", "ANTHROPIC_API_KEY", "\"${localProperties["ANTHROPIC_API_KEY"]}\"")
```

### Step 3 — Bundle AAPT2
Download AAPT2 binary for ARM64 from the Android SDK build-tools and place at:
```
app/src/main/assets/tools/aapt2
```

### Step 4 — Build
```bash
./gradlew assembleDebug
```

### Step 5 — Install on device (Android 11+)
```bash
adb install app/build/outputs/apk/debug/app-debug.apk
```

---

## 🗺️ v1 Milestones for Jules

Jules should implement in this order:

| # | Milestone | Description |
|---|---|---|
| 1 | **Project scaffold** | App skeleton, theme, navigation, home screen, new project wizard |
| 2 | **File system + editor** | File tree, Sora editor integration, tab management, symbol bar |
| 3 | **Build pipeline** | Full AAPT2 → Kotlin compile → D8 → APK → install flow with log output |
| 4 | **Git integration** | JGit wrapper, commit/push/pull/branch UI |
| 5 | **AI autocomplete** | Claude API FIM completions with ghost text in Sora |
| 6 | **AI chat panel** | Floating assistant with code-aware context |
| 7 | **Compose preview** | Screenshot-based @Preview renderer via isolated process |
| 8 | **Terminal** | Basic PTY terminal using ProcessBuilder |
| 9 | **Settings** | All preferences wired up |
| 10 | **Polish** | Animations, edge cases, error handling, tablet layout |

---

## 🧠 Architecture Notes for Jules

- **MVI pattern**: Each screen has a `UiState` data class, a `UiEvent` sealed class, and a `SideEffect` channel
- **Single Activity**: `MainActivity` hosts the entire `DroidForgeNavHost`. No fragments.
- **Build runs in background**: Always `Dispatchers.IO`, never block main thread. Emit `StateFlow<BuildState>` updates.
- **File system**: All project files live in `Context.filesDir/projects/{projectName}/`. Never use external storage as primary.
- **No root required**: All features must work on standard non-rooted Android 11+ devices.
- **Tablet support**: Use `WindowSizeClass` to show persistent side panels on tablets instead of drawers.

---

## 📄 License

```
Copyright 2025 DroidForge

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
```

---

*Built with 🔨 DroidForge — the IDE that fits in your pocket.*
