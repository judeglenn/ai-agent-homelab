# AI Agent Homelab — Personal Assistant Stack

A self-hosted autonomous AI agent running 24/7 on a repurposed MacBook Pro,
integrating multiple LLM providers, local voice transcription, and messaging
interfaces for personal productivity and nonprofit operations.

> Built and operated by Jude Glenn, Systems Engineer II

---

## Architecture Overview

### Messaging Interfaces

| Interface | Transport | Notes |
|-----------|-----------|-------|
| **Telegram** | Primary channel | Bidirectional; supports voice messages, file attachments, inline buttons |
| **iMessage** | BlueBubbles webhook | Dedicated Apple ID maintains privacy boundary between personal and agent identity |

### LLM Provider Fallback Chain

```
Claude Sonnet 4 (primary)
    → GPT-4o
        → Gemini Flash
            → Claude Haiku
```

Multi-provider architecture for rate limit resilience and cost optimization.
Provider order is tunable per session; model aliases enable seamless switching.

### Voice Pipeline

- **Transcription:** Local inference via `whisper.cpp` (GGML base English model) — no API calls, fully offline
- **Fallback:** Python `whisper` if CLI unavailable
- **TTS:** ElevenLabs in `tagged` mode — audio only on explicit request, minimizing credit burn
- **Audio handling:** `ffmpeg` for format conversion; `mediaLocalRoots` scoped to `/tmp/openclaw`

### Cost Optimization

- Context auto-compaction at 60k → 40k tokens
- Tagged TTS mode (voice on demand, not by default)
- Gemini Flash and Claude Haiku as cost-efficient fallback tiers
- Isolated cron sessions to avoid burning main session context on background tasks

### Automation

- **OpenClaw Cron** — persistent job scheduler, survives restarts
- Jobs run as isolated agent sessions with configurable delivery targets
- Example: weekly grant research scan delivers to Telegram + appends to Google Doc

---

## Notable Contributions to OpenClaw

### Bug Fix — [Issue #45540](https://github.com/openclaw/openclaw/issues/45540)

Diagnosed a TDZ (Temporal Dead Zone) initialization bug where `ANTHROPIC_MODEL_ALIASES`
was referenced before initialization, causing silent failures in TTS and config loading
while the gateway appeared healthy in `openclaw status`.

The failure mode was subtle: the gateway ran without errors, voice continued working
at the OS level, but model alias resolution and TTS delivery silently dropped. Traced
it through gateway logs to the uninitialized constant, filed a clean reproduction, and
the fix was confirmed and merged within 24 hours.

**Follow-up:** Filed [Issue #45552](https://github.com/openclaw/openclaw/issues/45552)
as a feature request for `openclaw doctor --check-config` to surface this class of
silent initialization failure proactively rather than requiring log diving.

### Kernel Panic Diagnosis

Identified the root cause of repeated kernel panics on Intel Mac hardware (MacBook Pro
16,2, macOS Tahoe 26) as an interaction between:

1. OpenClaw gateway node process spiking to 104% CPU (caused by the TDZ bug loop)
2. DisplayLink 15.1.0 driver at 52% CPU on boot

The combined load triggered four crashes in under 90 minutes. Applied `pmset` mitigations
(`powernap=0`, `gpuswitch=0`) and identified the correct DisplayLink version (15.0,
Dec 2025) compatible with Tahoe 26. Documented the interaction for other users running
DisplayLink + node-heavy workloads on Intel hardware.

---

## Real-World Applications

### Nonprofit Grant Operations

Deployed the agent for grant operations work on behalf of a volunteer-led
2SLGBTQIA+ outdoor recreation nonprofit in the Pacific Northwest
(newly independent 501(c)(3), 2026).

**What was built:**

- Extracted org data from existing grant applications to build a structured grant writing reference
- Researched and prioritized a funder prospect list across LGBTQ+, outdoor equity, PNW regional, and emerging org categories
- Set up Google Drive/Docs API integration (via `gogcli` + OAuth2) enabling direct in-place document editing
- Published a shared living grant strategy document, co-edited by volunteer leadership
- Configured a weekly Monday cron job to scan for new grant opportunities and append findings to the shared doc automatically

**Stack used for this workflow:**
`OpenClaw` + `gogcli` + `Google Drive API` + `Google Docs API` + `OpenClaw Cron` + `Telegram`

---

## Full Stack

| Component | Role |
|-----------|------|
| [OpenClaw](https://github.com/openclaw/openclaw) | Agent framework, gateway, cron scheduler |
| Anthropic Claude Sonnet | Primary LLM |
| OpenAI GPT-4o | Fallback LLM tier 2 |
| Google Gemini Flash | Fallback LLM tier 3 |
| Anthropic Claude Haiku | Fallback LLM tier 4 |
| whisper.cpp | Local audio transcription |
| ffmpeg | Audio format handling |
| ElevenLabs | Text-to-speech (tagged mode) |
| BlueBubbles | iMessage bridge |
| gogcli | Google Workspace CLI (Drive, Docs, Gmail, Calendar, Sheets) |
| macOS (Intel) | Host OS — MacBook Pro 16,2, 16GB RAM |

---

## Security Design

- Dedicated Apple ID for iMessage interface — agent identity separate from personal identity
- Elevated exec permissions scoped to specific Telegram chat ID only
- OAuth2 credentials stored locally, never committed
- All API keys and personal identifiers redacted from this repository
- `GOG_ACCOUNT` environment variable pattern keeps credentials out of command history

---

## Setup Notes

Configuration lives in `~/.openclaw/openclaw.json`. Key lessons learned:

- `models.providers.*` requires `baseUrl` (string) and `models` (array) even for built-in providers — validate schema before writing
- Built-in providers (`openai`, `google`) use `auth.profiles` — do not add custom provider blocks for these
- Always keep a backup before editing config; `openclaw doctor --fix` repairs some issues but not all
- Use `openclaw status` and gateway logs at `/tmp/openclaw/` for diagnosing silent failures

---

*This repo documents a working personal AI infrastructure stack. Configs are provided as
examples with all sensitive values redacted. See comments in each file.*
