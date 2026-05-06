# CLAUDE.md

This file is the working agreement and project prompt for Claude Code when developing **NativeTavern HarmonyOS**.

> Preferred language for explanations and task reports: Chinese.
> Code, identifiers, file names, comments that are already English should remain English unless a localized UI string is being added.

---

## 0. One-shot prompt to start a Claude Code session

Paste this at the beginning of a new Claude Code session:

```text
请先阅读 CLAUDE.md、README.md、pubspec.yaml，以及当前仓库结构。你的任务是推进 NativeTavern 的 HarmonyOS 6.1 原生迁移，但不要破坏现有 Flutter/iOS/Android 主项目。

本阶段目标不是一次性复刻 98% SillyTavern 功能，而是先交付一个可真机运行、可上架验证的 HarmonyOS Alpha：
1. ArkTS/ArkUI 原生工程能启动；
2. 用户可配置 OpenAI-Compatible / OpenRouter / Ollama 类接口；
3. 支持非流式和 SSE 流式聊天；
4. 支持导入基础角色 JSON 和 PNG V2/V3 角色卡；
5. 支持本地 SQLite/relationalStore 保存会话、消息和角色；
6. 提供基础设置页、角色页、对话页；
7. 不做 Google 登录/Google Drive，同步先用本地备份或 WebDAV 预留接口；
8. 每次修改要小步提交，先说明计划，再实现，再运行可用检查，并在最后报告完成内容、未完成内容和下一步建议。

请先做仓库体检：列出现有结构、可复用模块、风险点和第一批需要创建/修改的文件。未经确认不要大规模重构现有 Flutter 代码。
```

---

## 1. Project identity

NativeTavern is a native mobile SillyTavern-compatible client. The current main implementation is Flutter + Dart + Rust FFI for iOS and Android.

The new workstream is **NativeTavern HarmonyOS**, a HarmonyOS 6.1 native port using ArkTS/ArkUI, relationalStore, and NAPI/Rust where useful.

The HarmonyOS port should be treated as a sibling implementation, not a replacement for the current Flutter app.

---

## 2. Strategic goal

Build the best practical SillyTavern-compatible AI roleplay/chat client for HarmonyOS NEXT devices.

The project should validate real user demand before attempting full feature parity. Prioritize a stable, usable Alpha over broad feature checklists.

### Product positioning

- Native HarmonyOS client for role-card based AI chat.
- Compatible with common SillyTavern data formats.
- Bring-your-own-key: users configure their own LLM endpoints.
- Local-first: conversations, characters, settings, and backups should work without a cloud account.
- Open-source under AGPL-3.0, consistent with the existing project.

---

## 3. Non-negotiable rules

1. **Do not break the existing Flutter app.**
   - Existing `lib/`, `android/`, `ios/`, `pubspec.yaml`, and Rust code should remain usable.
   - The HarmonyOS port should live in `harmony/` unless a DevEco project has already been created elsewhere.

2. **Do not store secrets.**
   - Never commit API keys, tokens, passwords, private endpoints, signing keys, or user data.
   - Add placeholders and documentation instead.

3. **Do not claim full compatibility before tests.**
   - Mark partial implementations honestly.
   - Keep a `TODO` or implementation status section when a feature is only scaffolded.

4. **Small, reviewable changes.**
   - Prefer incremental patches.
   - Avoid sweeping rewrites unless explicitly requested.

5. **Use robust error handling for network and streaming.**
   - Streaming chat must handle cancellation, timeouts, malformed chunks, network errors, and provider-specific end markers.

6. **Respect AGPL-3.0.**
   - Keep license headers or notices when appropriate.
   - Do not copy incompatible code.

---

## 4. Target architecture for HarmonyOS port

Preferred directory layout:

```text
harmony/
├── entry/src/main/
│   ├── ets/
│   │   ├── entryability/
│   │   ├── pages/
│   │   │   ├── Index.ets
│   │   │   ├── ChatPage.ets
│   │   │   ├── CharacterPage.ets
│   │   │   ├── CharacterEditorPage.ets
│   │   │   └── SettingsPage.ets
│   │   ├── components/
│   │   │   ├── ChatBubble.ets
│   │   │   ├── StreamingText.ets
│   │   │   └── CharacterCard.ets
│   │   ├── services/
│   │   │   ├── llm/
│   │   │   │   ├── LLMProvider.ets
│   │   │   │   ├── LLMService.ets
│   │   │   │   ├── OpenAICompatibleProvider.ets
│   │   │   │   ├── OpenRouterProvider.ets
│   │   │   │   ├── OllamaProvider.ets
│   │   │   │   └── SSEClient.ets
│   │   │   ├── database/
│   │   │   │   ├── DatabaseManager.ets
│   │   │   │   ├── ChatRepository.ets
│   │   │   │   ├── CharacterRepository.ets
│   │   │   │   └── MigrationManager.ets
│   │   │   ├── import/
│   │   │   │   ├── CharacterImportService.ets
│   │   │   │   └── PngCardParser.ets
│   │   │   └── napi/
│   │   ├── models/
│   │   │   ├── ChatMessage.ets
│   │   │   ├── Conversation.ets
│   │   │   ├── Character.ets
│   │   │   └── LLMConfig.ets
│   │   ├── store/
│   │   │   ├── ChatStore.ets
│   │   │   ├── CharacterStore.ets
│   │   │   └── SettingsStore.ets
│   │   └── utils/
│   ├── cpp/
│   └── resources/
├── rust/
├── oh-package.json5
├── build-profile.json5
└── module.json5
```

If DevEco Studio requires a different layout, document the reason in the implementation notes.

---

## 5. Alpha scope

The Alpha is successful when a user can install the app on a HarmonyOS device and complete this path:

1. Launch the app.
2. Open Settings.
3. Configure an OpenAI-compatible endpoint, model name, API key, and base URL.
4. Import or create a character.
5. Start a conversation.
6. Receive a streaming model response.
7. Close and reopen the app.
8. See the conversation, character, and settings persisted locally.
9. Export a basic local backup.

### Alpha must-have features

- ArkTS/ArkUI app shell.
- Navigation between Home, Chat, Character, and Settings pages.
- OpenAI-compatible non-streaming and streaming chat.
- OpenRouter support if it can reuse the OpenAI-compatible path.
- Ollama support if local network permission and base URL handling are available.
- Basic role card import:
  - JSON character card first.
  - PNG V2/V3 metadata support via ArkTS parser or NAPI/Rust parser.
- relationalStore database:
  - conversations
  - chat_messages
  - characters
  - llm_configs or settings
- Basic Markdown rendering, even if limited.
- Cancellation of an active streaming request.
- Error messages visible to the user.

### Alpha non-goals

Do not implement these until the Alpha path is stable:

- Full 18-theme editor.
- TTS/STT.
- Translation providers.
- Image generation.
- Google Sign-In or Google Drive sync.
- Full group chat.
- Full World Info recursion and scoring.
- Full regex scripting engine.
- Full variable system.
- Full prompt preset parity.
- Extension ecosystem.

---

## 6. Beta scope

After Alpha is stable, implement these in priority order:

1. Prompt manager and SillyTavern preset import.
2. World Info / lorebook with keyword matching.
3. Full character editor.
4. CharX import/export.
5. Swipe alternatives and regenerate/edit/delete message operations.
6. Group chat.
7. Bookmarks and branches.
8. Macro system.
9. Regex scripts.
10. Local variables and chat variables.
11. WebDAV or local backup/restore workflow.

---

## 7. Provider implementation guidance

Design LLM support around a provider interface, for example:

```typescript
export interface LLMProvider {
  name: string;
  testConnection(config: LLMConfig): Promise<string>;
  generate(messages: ChatMessage[], config: LLMConfig): Promise<LLMResponse>;
  stream(messages: ChatMessage[], config: LLMConfig, callbacks: StreamCallbacks): Promise<void>;
}
```

Start with these providers:

1. OpenAI-compatible: highest priority.
2. OpenRouter: usually same request format, different defaults/headers.
3. Ollama: useful for local users and self-hosted workflows.
4. Claude and Gemini: Beta unless Alpha is already stable.

Provider code should avoid hardcoding one vendor into the core chat flow.

---

## 8. SSE streaming guidance

Streaming is a core feature and should not be treated as a toy demo.

Implementation requirements:

- Parse HTTP response headers before processing event body.
- Handle `Transfer-Encoding: chunked` correctly when using raw sockets.
- Handle partial chunks and multi-byte UTF-8 boundaries.
- Handle SSE lines including `data:`, blank-line event separators, and `[DONE]` style sentinels.
- Provider-specific parsing should live outside the transport layer.
- Support cancellation.
- Support timeout and user-visible retry/error messages.
- Avoid unbounded buffer growth.
- Add unit-like parser tests where possible.

If HarmonyOS exposes a reliable streaming HTTP API in the installed SDK, prefer the platform API over raw socket code and document the decision.

---

## 9. Database guidance

Use `@ohos.data.relationalStore` for the HarmonyOS port.

Minimum Alpha tables:

```sql
conversations(id, character_id, title, created_at, updated_at, last_message_at, message_count)
chat_messages(id, conversation_id, role, content, reasoning_content, created_at, updated_at, model, parent_id, swipe_index)
characters(id, name, description, personality, scenario, first_mes, mes_example, avatar_path, raw_json, created_at, updated_at)
settings(key, value, updated_at)
llm_configs(id, name, provider, base_url, model, api_key_ref, temperature, top_p, max_tokens, created_at, updated_at)
```

Rules:

- Do not store API keys in plain text if HarmonyOS secure storage is available.
- Add migration versioning from the beginning.
- Keep repository classes thin and testable.
- Never build SQL by string-concatenating user input.

---

## 10. Character card compatibility

Priority order:

1. JSON character card import.
2. PNG V2 `chara` metadata import.
3. PNG V3 `ccv3` metadata import.
4. PNG export.
5. CharX import.
6. CharX export.

Use the existing Rust parser when it reduces risk, but do not introduce NAPI complexity before the basic Alpha flow works.

---

## 11. UI guidance

The Alpha UI should be simple and stable.

Required pages:

- Home / conversation list.
- Chat page.
- Character list.
- Character import/create/edit page.
- Settings page.

Design principles:

- Mobile-first layout.
- Clear empty states.
- Visible loading/streaming/cancel states.
- User-readable error messages.
- Do not spend Alpha time on detailed theme editing.

---

## 12. Development workflow for Claude Code

For every task:

1. Inspect relevant files first.
2. Summarize what exists.
3. State the next small implementation goal.
4. Make the smallest coherent code change.
5. Run available checks.
6. Report:
   - files changed
   - behavior implemented
   - commands run
   - tests/checks passed or unavailable
   - known limitations
   - next recommended step

When blocked by missing SDK, missing device, or unsupported local command, do not fake success. Document the blocker and produce the best code-level patch possible.

---

## 13. Suggested first implementation sequence

1. Add `harmony/` project skeleton.
2. Add app shell and navigation.
3. Add models: `ChatMessage`, `Conversation`, `Character`, `LLMConfig`.
4. Add settings storage.
5. Add relationalStore database and repositories.
6. Add non-streaming OpenAI-compatible provider.
7. Add Chat page wired to non-streaming provider.
8. Add SSE transport and parser tests.
9. Wire streaming response into Chat page.
10. Add JSON character import.
11. Add PNG metadata import.
12. Add backup export.
13. Add manual test checklist.

---

## 14. Definition of done for Alpha

Alpha is done only when all of these are true:

- App launches on HarmonyOS emulator or device.
- User can configure an OpenAI-compatible endpoint.
- User can send a message and receive a response.
- Streaming response is visible incrementally.
- User can cancel a stream.
- Conversations persist after restart.
- At least JSON character import works.
- At least one role-card PNG path is implemented or clearly marked as pending.
- README documents how to build/run the HarmonyOS port.
- No secrets are committed.
- Known limitations are documented.

---

## 15. Commit message style

Use concise commit messages:

```text
harmony: add ArkUI app shell
harmony: implement OpenAI-compatible provider
harmony: add relationalStore repositories
harmony: implement SSE parser
harmony: add character JSON import
```
