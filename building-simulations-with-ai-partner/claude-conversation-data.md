# Claude Code Conversation Data Reference

Reference for generating graphs and summaries from saved Claude Code conversations.

## Storage Location

- **Base path:** `~/.claude/projects/`
- **Structure:** One subdirectory per project, named by encoded path (e.g., `-Users-scytacki-Development-ocean-explorer`)
- **Files:** Each conversation session is a separate `.jsonl` file named by session UUID
- **Subagents:** Stored in `subagents/` subdirectories (typically excluded from analysis)
- **Global history:** `~/.claude/history.jsonl` — lightweight log of commands across all projects

## Date Range

Earliest recorded conversations: **2026-01-24**

## Projects with Conversations

As of 2026-02-14, 173 conversation files across 18 projects:

- codap-main
- collaborative-learning (+ worktrees: history-view, seq-history, toolbar-refactor)
- dev-templates (+ dev-templates-scripts)
- devtools-frames-inspector (+ worktrees: feature-observable-messages-computed-frameid, feature-point-of-view)
- google-docs-mcp (MCP server setup)
- grist-widget
- ocean-explorer (+ worktrees: phase-3-cgrid)
- post-message-plugin
- presentations
- s3-deploy-action
- starter-projects

## JSONL Record Types

Each line in a `.jsonl` file is a JSON object with a `type` field:

| Type | Description |
|------|-------------|
| `user` | User message |
| `assistant` | Claude response |
| `progress` | Intermediate tool execution progress |
| `file-history-snapshot` | Snapshot of tracked file states |
| `system` | System messages |
| `queue-operation` | Session queue management |

## Key Message Fields

### Common fields (user & assistant)

| Field | Description |
|-------|-------------|
| `type` | `"user"` or `"assistant"` |
| `uuid` | Unique message ID |
| `parentUuid` | Links to previous message (for threading) |
| `timestamp` | ISO 8601 format, e.g. `"2026-02-14T12:19:35.819Z"` |
| `sessionId` | UUID matching the filename |
| `cwd` | Working directory at time of message |
| `gitBranch` | Active git branch |
| `version` | Claude Code version string |
| `isSidechain` | Boolean — whether this is a branched conversation |
| `message` | The actual message content (see below) |

### User message extras

| Field | Description |
|-------|-------------|
| `permissionMode` | Permission mode active (e.g. `"default"`) |
| `todos` | Todo list state at time of message |
| `thinkingMetadata` | Includes `maxThinkingTokens` |

### Assistant `message` object

| Field | Description |
|-------|-------------|
| `role` | `"assistant"` |
| `model` | Model ID used (e.g. `"claude-opus-4-6"`, `"claude-sonnet-4-5-20250929"`) |
| `content` | Array of content blocks (see below) |
| `usage` | Token usage for this response |
| `stop_reason` | Why generation stopped |

### Content block types (in `message.content`)

- **`text`** — Text output. Keys: `type`, `text`
- **`tool_use`** — Tool invocation. Keys: `type`, `id`, `name`, `input`, `caller`

### Token usage (in `message.usage`)

| Field | Description |
|-------|-------------|
| `input_tokens` | Non-cached input tokens |
| `cache_creation_input_tokens` | Tokens written to cache |
| `cache_read_input_tokens` | Tokens read from cache |
| `output_tokens` | Tokens generated |
| `service_tier` | e.g. `"standard"` |

## Useful Analysis Ideas

- **Activity timeline:** Plot messages by timestamp across sessions/projects
- **Token usage over time:** Sum input/output tokens per session or day
- **Model usage:** Track which models were used when
- **Tool usage frequency:** Count tool_use blocks by `name` field
- **Project activity:** Compare conversation counts and message volumes across projects
- **Session duration:** First to last timestamp within a session
- **Conversation patterns:** Ratio of user messages to assistant messages

## Parsing Notes

- Filter to `type == "user"` or `type == "assistant"` for actual conversation messages
- Exclude `subagents/` paths unless analyzing agent delegation patterns
- The `parentUuid` chain reconstructs message ordering within a session
- Some assistant messages are streamed incrementally — the final message has the complete `content` and `usage`
- Token costs are not directly recorded; they can be estimated from `usage` fields and model pricing
