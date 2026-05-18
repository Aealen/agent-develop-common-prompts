# Profile: 对话交互类 Agent

适用于面向终端用户的聊天/语音交互 Agent。

---

## [PROFILE: CONVERSATIONAL]

```
Applies to: Agents that interact with end users through chat/voice interfaces
Examples: customer service bot, voice assistant, technical support agent, FAQ bot
```

## 附加需求

### 追加到 [TASK] 部分

```
ADDITIONAL_REQUIREMENTS:
- Must include session management (create, resume, expire sessions)
- Must implement rate limiting per user/session
- Must sanitize all user input before processing (injection prevention)
- Must support conversation context window management (trim old messages)
- Must distinguish user-facing error messages from internal error logs
- Must support multi-turn conversation with context persistence
```

### 追加到 [OUTPUT_FORMAT] 部分

```
Additional sections in output:

## Session Management
| Feature | Behavior |
|---------|----------|
| Session creation | New session on first message or explicit start command |
| Session resume | Match user_id + session_id, restore context from StateManager |
| Session expiry | TTL-based (configurable), auto-cleanup on next request |
| Session limit | Max concurrent sessions per user (configurable) |

## Context Window Strategy
- Max tokens per context window: configurable
- Trimming strategy: keep system prompt + last N turns + summary of earlier turns
- Summary generation: when context exceeds threshold, summarize oldest turns

## Response Safety
- Input sanitization: strip/escape HTML, JS, SQL injection patterns
- Output filtering: no internal state exposure in user-facing responses
- Content policy: configurable rules for allowed/disallowed response content
```

### 追加到 [CONSTRAINTS] 部分

```
CONVERSATIONAL_CONSTRAINTS:
- Never expose internal state, logs, or error details in user-facing messages
- Never store raw user messages in plain text (encrypt or hash PII)
- Never allow unlimited message rate per user (DDoS protection)
- Must validate and sanitize every user input before processing
- Must handle malformed/empty/oversized input gracefully
- User-facing error messages must be friendly and actionable (not technical)
- Must support graceful degradation when LLM/API backend is slow or unavailable
```

### 追加到 Harness 要求

```
MessageLoop additions:
- Must handle concurrent sessions (one message loop per session or multiplexed)
- Must implement per-session timeout (auto-close idle sessions)
- Must support async response streaming if applicable

StateManager additions:
- Must persist conversation context per session
- Must support session-level checkpoint (not just global state)
- Must clean up expired session data

ErrorHandler additions:
- Must distinguish: user input error vs backend error vs system error
- User input error → friendly error message, keep session alive
- Backend error (LLM unavailable) → fallback response, keep session alive
- System error → terminate session gracefully, notify user

EventEmitter additions:
- Must NOT log raw user messages in events (PII protection)
- Must mask or hash sensitive fields (email, phone, address) in events
- Must emit session lifecycle events: session_created, session_expired, session_closed
```
