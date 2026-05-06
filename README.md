# NativeTavern

<p align="center">
  <a href="README.md">简体中文</a> | <a href="README.en.md">English</a>
</p>

<p align="center">
  🌐 <a href="https://nativetavern.com/">Native Tavern</a>
</p>

NativeTavern 是一个原生跨平台移动应用，目标是将 SillyTavern 的核心体验重新实现为高性能移动端客户端，并尽可能兼容 SillyTavern 的角色卡、预设、聊天和扩展数据格式。

当前主线实现为 **Flutter + Dart + Rust FFI**，支持 iOS / Android。仓库同时启动 **HarmonyOS 6.1 原生迁移计划**，目标是在鸿蒙 NEXT 设备上提供 ArkTS/ArkUI 原生版本。

---

## 当前状态

| 方向 | 状态 | 说明 |
|---|---|---|
| Flutter iOS / Android 主项目 | ✅ 可用 | 当前主要实现，功能完成度较高 |
| Rust 原生核心 | ✅ 可用 | 用于 PNG 角色卡、CharX 等能力 |
| HarmonyOS 原生迁移 | 🚧 Alpha 规划中 | 使用 ArkTS/ArkUI、relationalStore、NAPI/Rust |
| Claude Code 开发指引 | ✅ 已加入 | 见 [`CLAUDE.md`](CLAUDE.md) |

---

## HarmonyOS 6.1 原生迁移计划

鸿蒙 NEXT 移除了 Android 兼容层，Flutter APK 无法作为长期可依赖的鸿蒙分发方案。NativeTavern 因此启动 HarmonyOS 原生迁移工作流。

迁移原则：

- **不破坏现有 Flutter 主项目**：鸿蒙版本作为并行实现推进。
- **不追求一口气 98% 功能对等**：先做稳定、可真机运行、可上架验证的 Alpha。
- **优先验证真实用户价值**：先完成角色卡 + BYOK LLM + 流式聊天 + 本地存储的闭环。
- **本地优先**：角色、聊天、设置和备份尽量本地保存，云同步后置。
- **AGPL-3.0 开源合规**：延续当前许可证。

### HarmonyOS Alpha 目标

Alpha 阶段完成后，用户应能在鸿蒙设备上完成以下流程：

1. 启动 App。
2. 在设置页配置 OpenAI-Compatible / OpenRouter / Ollama 类接口。
3. 导入或创建角色。
4. 发起对话。
5. 看到 SSE 流式回复。
6. 关闭并重新打开 App 后，角色、设置和聊天记录仍然存在。
7. 导出基础本地备份。

### Alpha 必做功能

- ArkTS/ArkUI 应用壳和基础导航。
- 首页、对话页、角色页、设置页。
- OpenAI-Compatible 非流式和流式聊天。
- OpenRouter 复用 OpenAI-Compatible 路径。
- Ollama 基础支持。
- JSON 角色卡导入。
- PNG V2/V3 角色卡元数据导入。
- relationalStore 本地数据库。
- 基础 Markdown 渲染。
- 流式生成取消、超时、错误提示。
- 基础本地备份导出。

### Alpha 暂不做

- 完整主题编辑器。
- TTS / STT。
- 翻译。
- 图像生成。
- Google 登录 / Google Drive 同步。
- 完整群聊。
- 完整 World Info 递归和评分。
- 完整正则脚本、变量系统、扩展生态。

### 推荐鸿蒙目录结构

```text
harmony/
├── entry/src/main/
│   ├── ets/
│   │   ├── entryability/
│   │   ├── pages/
│   │   ├── components/
│   │   ├── services/
│   │   │   ├── llm/
│   │   │   ├── database/
│   │   │   ├── import/
│   │   │   └── napi/
│   │   ├── models/
│   │   ├── store/
│   │   └── utils/
│   ├── cpp/
│   └── resources/
├── rust/
├── oh-package.json5
├── build-profile.json5
└── module.json5
```

Claude Code 的详细执行规范、提示词和阶段计划见 [`CLAUDE.md`](CLAUDE.md)。

---

## 截图

<p align="center">
  <img src="photo/Chat.png" width="200" alt="聊天界面"/>
  <img src="photo/Character.png" width="200" alt="角色界面"/>
  <img src="photo/AiConfig.png" width="200" alt="AI配置"/>
</p>

<p align="center">
  <img src="photo/AIPreset.png" width="200" alt="AI预设"/>
  <img src="photo/PromptManager.png" width="200" alt="提示词管理"/>
  <img src="photo/Wordbook.png" width="200" alt="世界信息/知识库"/>
</p>

| 聊天 | 角色 | AI配置 |
|:---:|:---:|:---:|
| 实时流式聊天，支持消息操作 | 角色卡片，包含头像和详情 | 多提供商 LLM 配置 |

| AI预设 | 提示词管理 | 世界信息 |
|:---:|:---:|:---:|
| 导入 SillyTavern 预设 | 自定义提示词排序 | 基于关键词的上下文注入 |

---

## 功能特性

### 核心功能 ✅

- 📱 **原生移动应用**：当前 Flutter 版本支持 iOS 和 Android。
- ⚡ **移动端优化**：围绕手机和平板使用体验设计。
- 🤖 **多提供商 LLM 支持**：OpenAI、Claude、OpenRouter、Gemini、Ollama、KoboldCpp 等。
- 📦 **SillyTavern 数据兼容**：支持 PNG 角色卡、CharX、JSON、ST 预设等格式。
- 💬 **实时流式响应**：支持主流 Provider 的 SSE 流式传输。

### 角色管理 ✅

- 📥 导入 PNG V2/V3、CharX、JSON。
- 📤 导出 PNG V3、CharX、JSON。
- ✏️ 创建和编辑角色字段。
- 🖼️ 自定义头像。
- 📚 CharX 嵌入式知识库支持。

### 聊天功能 ✅

- 💬 编辑、删除、重新生成消息。
- 🔀 支持滑动备选回复。
- 👥 群聊与多角色对话。
- 🔖 书签、检查点和分支对话。
- 📝 作者注释。
- 🎭 用户人设。
- 📄 HTML / Markdown 富文本渲染。

### 世界信息 / 知识库 ✅

- 🌍 关键词触发。
- 📍 多种注入位置。
- 🔄 递归扫描。
- 📊 优先级和分组评分。

### 提示词管理 ✅

- 📋 Prompt 管理器。
- 📥 SillyTavern 预设导入。
- 🎯 自定义 Prompt 区块。
- 📍 深度注入。

### 高级功能 ✅

- 🎛️ 完整采样器控制。
- 🧠 思维链 / reasoning 内容解析与显示。
- 🏷️ 角色标签。
- ⌨️ Markdown 输入快捷键。
- 🎭 表情精灵。
- 🔊 TTS。
- 🎤 STT。
- 🌐 翻译。
- 🎨 图像生成。
- 🔍 正则脚本。
- 🌐 全局和本地变量系统。
- 💾 聊天备份。
- ⚙️ 宏系统与斜杠命令。
- 🖼️ 聊天背景。

---

## 技术栈

### 当前 Flutter 主项目

| 组件 | 技术 |
|---|---|
| UI 框架 | Flutter / Dart |
| 状态管理 | Riverpod |
| 导航 | go_router |
| 数据库 | SQLite / Drift |
| 原生核心 | Rust FFI |
| HTTP 客户端 | Dio |
| 富文本 / Web | flutter_markdown / flutter_inappwebview |

### HarmonyOS 迁移目标

| 组件 | 技术 |
|---|---|
| UI 框架 | ArkTS / ArkUI |
| 状态管理 | ArkUI 状态装饰器 + 自定义 Store |
| 数据库 | @ohos.data.relationalStore |
| 网络 | @ohos.net.http / socket / TLS socket |
| 流式响应 | SSE parser |
| 原生桥接 | NAPI + C++ shim + Rust |
| 构建工具 | DevEco Studio / ohpm |

---

## 项目结构

```text
native_tavern/
├── lib/                    # Flutter / Dart 主项目代码
│   ├── main.dart
│   ├── app.dart
│   ├── core/
│   ├── data/
│   ├── domain/
│   └── presentation/
├── rust/                   # Rust 原生核心
├── ios/                    # iOS 平台代码
├── android/                # Android 平台代码
├── harmony/                # HarmonyOS 原生迁移代码，开发中
├── plans/                  # 架构与开发计划
├── CLAUDE.md               # Claude Code 开发指引
└── pubspec.yaml
```

---

## 开始使用：Flutter 版本

### 前置要求

- Flutter SDK >= 3.16.0
- Rust 工具链，部分原生能力需要
- Xcode，用于 iOS 开发
- Android Studio，用于 Android 开发

### 安装运行

```bash
git clone https://github.com/cnChenKai/NativeTavern.git
cd NativeTavern
flutter pub get
flutter run
```

构建 Rust 核心，按当前平台需求执行：

```bash
cd rust
cargo build --release
```

---

## 开始使用：HarmonyOS 版本

HarmonyOS 版本仍处于 Alpha 开发阶段。建议使用 DevEco Studio 打开 `harmony/` 目录，按 DevEco 工程提示安装 HarmonyOS SDK、ohpm 依赖和模拟器/真机调试环境。

当前建议工具链：

- DevEco Studio 5.0+
- HarmonyOS SDK / OpenHarmony SDK，对齐目标 API
- Node.js 18+
- Rust 1.75+
- 鸿蒙真机或模拟器

开发前请阅读：

```text
CLAUDE.md
```

---

## SillyTavern 兼容性

### 支持的导入格式

| 格式 | 描述 | Flutter 主项目 | HarmonyOS 目标 |
|---|---|---:|---:|
| PNG V2 | 带 `chara` tEXt 块的角色卡 | ✅ | 🚧 Alpha |
| PNG V3 | 带 `ccv3` tEXt 块的角色卡 | ✅ | 🚧 Alpha |
| CharX | 包含 card.json 和资源的 ZIP 归档 | ✅ | ⏳ Beta |
| JSON | 原始角色 JSON 导出 | ✅ | 🚧 Alpha |
| ST 预设 | SillyTavern AI 预设 JSON | ✅ | ⏳ Beta |

### 支持的导出格式

| 格式 | Flutter 主项目 | HarmonyOS 目标 |
|---|---:|---:|
| PNG V3 | ✅ | ⏳ Beta |
| CharX | ✅ | ⏳ Beta |
| JSON | ✅ | 🚧 Alpha |
| 本地备份 | ✅ | 🚧 Alpha |

---

## 开发路线

### Flutter 主项目

| 阶段 | 功能 | 状态 |
|---|---|---|
| 1-2 | 核心基础、聊天核心 | ✅ 完成 |
| 3A | 消息操作、人设、指令模式 | ✅ 完成 |
| 3B | 世界信息、CharX 完整导入、角色编辑器 | ✅ 完成 |
| 4A | 群聊、聊天书签 | ✅ 完成 |
| 4B | 宏系统 | ✅ 完成 |
| 5 | 作者注释、提示词管理、高级设置、快捷回复、主题、统计、思维链 | ✅ 完成 |
| 6 | 斜杠命令、标签、背景、HTML/Markdown | ✅ 完成 |
| 7 | 表情精灵、TTS、STT、翻译、图像生成 | ✅ 完成 |
| 8 | 正则脚本、变量、聊天备份 | ✅ 完成 |
| 9 | 扩展、RAG/向量 | ⏳ 计划中 |

### HarmonyOS 原生版本

| 阶段 | 目标 | 状态 |
|---|---|---|
| MVP 验证 | ArkTS UI、LLM、SSE、SQLite、Rust NAPI 技术验证 | ✅ 已验证 |
| Alpha | 可真机运行的基础聊天客户端 | 🚧 开发中 |
| Beta | Prompt、World Info、CharX、群聊、消息高级操作 | ⏳ 计划中 |
| Release | 打磨、测试、性能优化、上架准备 | ⏳ 计划中 |

---

## 与 SillyTavern 功能对比

| 功能 | SillyTavern Web | NativeTavern Flutter | HarmonyOS 目标 |
|---|---:|---:|---:|
| 角色导入/导出 | ✅ | ✅ | 🚧 |
| LLM Provider | 10+ | 核心 Provider | 🚧 |
| 流式传输 | ✅ | ✅ | 🚧 Alpha |
| 消息操作 | ✅ | ✅ | ⏳ Beta |
| 群聊 | ✅ | ✅ | ⏳ Beta |
| World Info | ✅ | ✅ | ⏳ Beta |
| Prompt 管理 | ✅ | ✅ | ⏳ Beta |
| 宏 | ✅ | ✅ | ⏳ Beta |
| 主题 | ✅ | ✅ | ⏳ Beta |
| 斜杠命令 | ✅ | ✅ | ⏳ Beta |
| HTML / Markdown | ✅ | ✅ | 🚧 Alpha |
| TTS / STT | ✅ | ✅ | ⏳ Later |
| 翻译 | ✅ | ✅ | ⏳ Later |
| 图像生成 | ✅ | ✅ | ⏳ Later |
| 正则脚本 | ✅ | ✅ | ⏳ Beta |
| 变量系统 | ✅ | ✅ | ⏳ Beta |
| 聊天备份 | ✅ | ✅ | 🚧 Alpha |
| 扩展 | ✅ | ⏳ | ⏳ Later |

---

## 使用 Claude Code 开发

本仓库已经准备了 [`CLAUDE.md`](CLAUDE.md)，用于指导 Claude Code 进行 HarmonyOS 原生迁移。

推荐流程：

1. 在 Claude Code 中打开本仓库。
2. 先让 Claude 阅读 `CLAUDE.md`、`README.md`、`pubspec.yaml` 和仓库结构。
3. 要求 Claude 先做仓库体检，不要立即大规模重构。
4. 按 `CLAUDE.md` 中的 Alpha 顺序逐步实现。
5. 每轮任务结束后要求 Claude 报告文件变更、检查命令、已知限制和下一步。

推荐开场提示词见 `CLAUDE.md` 第 0 节。

---

## 许可证

AGPL-3.0 - 详见 [LICENSE](LICENSE)。

---

## 致谢

- [SillyTavern](https://github.com/SillyTavern/SillyTavern) - 原始 Web 项目
- [Flutter](https://flutter.dev) - 跨平台 UI 框架
- [Riverpod](https://riverpod.dev) - 状态管理
- HarmonyOS / OpenHarmony 开发生态
