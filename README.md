# Claude Thinking Translator

一个 Claude Code mod：把 Claude 的**思考内容**翻译成你的语言，终端和桌面 App 通用。

- 思考原文保留不动，译文显示在思考块下方，用 **Markdown** 渲染（列表、表格、代码块都正常）
- 后台预翻译，不阻塞思考和回答的显示；点击「展开思考译文」即可查看
- **默认使用微软免费翻译**，无需 API key
- 代码块、日志、堆栈、JSON、diff 等技术内容不送翻，原样保留
- 支持 `/translate` 开关，状态会保存
- 没有外部依赖，翻译接口通过 `curl` 直连

> 说明：Claude Code 会把模型签名过的原始思考块写进转录，插件无法改写它。因此本插件不改写思考块，而是在它下方追加一个属于插件自己的译文块。这是目前唯一能稳定显示思考译文的方式。

## 环境要求

- Claude Code v2.1.287 或更高（`claude --version`）
- 使用 `microsoft` 或 `openai` provider 时，系统里需要有 `curl`（Windows 10+、macOS、Linux 自带）

## 安装

```bash
claude plugin marketplace add wanlang0118/claude-thinking-translator
claude plugin install thinking-translator@claude-thinking-translator
```

重启 Claude Code，或新开一个会话后生效。

开发时可以直接加载本地目录：

```bash
claude --plugin-dir ./claude-thinking-translator
```

## 使用

| 命令 | 作用 |
|---|---|
| `/translate` | 显示或隐藏思考译文 |
| `/translate on` / `off` | 启用或关闭翻译服务（关闭时会取消进行中的请求） |

翻译在原文绑定到消息后自动开始；完成前按钮显示「思考翻译中…」，完成后变为「展开思考译文」。翻译失败时按钮显示「翻译失败，点击重试」。

## 配置

在 `/config` 里修改，或写入 `settings.json` 的 `pluginConfigs["thinking-translator@claude-thinking-translator"]`。

| 配置项 | 默认值 | 说明 |
|---|---|---|
| `show_by_default` | `true` | 默认显示思考译文 |
| `lang` | `zh-Hans` | 目标语言（微软语言码） |
| `provider` | `microsoft` | `microsoft` / `session` / `openai` |
| `model` | `haiku` | `session` 或 `openai` 使用的模型 |
| `base_url` | `http://127.0.0.1:8021/v1` | `openai` 的地址 |
| `api_key` | 空 | `openai` 的密钥（敏感项） |

### 翻译服务怎么选

- **microsoft（默认）**：免费、快，无需配置。**思考文本会发给微软的翻译服务**，里面可能有代码片段和路径。处理敏感项目时不要用。
- **session**：用本会话的凭证调模型翻译，文本不会离开 Claude，质量更好，但每个思考块会多一次模型请求，耗 token。
- **openai**：发给你配置的 OpenAI 兼容服务，适合本地模型（如 Ollama、llama.cpp）或自己的 API。

## 工作方式与限制

- **后台翻译，不阻塞**：`turn.step` 只收集思考原文并原样透传，思考显示零延迟；翻译在后台完成。
- **不改写思考块**：转录里保存的是模型签名的原文；译文是插件额外绘制的块，位置在思考块下方。
- **不翻译的内容**：围栏代码块和缩进代码块、日志、堆栈、JSON、diff、符号密度很高的内容；已经是目标语言的段落（中文目标用本地字符统计判断，其他语言交给翻译服务检测）。
- **缓存**：译文按消息 id 缓存在进程内存里，重启或 `--resume` 后会丢失（不会写入转录）。
- **表面支持**：终端和桌面 App；VS Code 和移动端不处理。
- **失败处理**：单段翻译失败会自动重试，仍失败则按钮提供手动重试。

## 开发

```bash
claude --plugin-dir ./          # 加载当前目录，改动后自动重载
claude plugin validate .claude-plugin/plugin.json
claude plugin validate .claude-plugin/marketplace.json
```

## License

[MIT](LICENSE)
