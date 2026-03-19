# Changelog

All notable changes and milestones for this project are documented here.

---

## [2026-03-16] — Initial Build & Launch

### Infrastructure
- Deployed OpenClaw on repurposed MacBook Pro 16,2 (Intel, 16GB RAM, macOS Tahoe 26)
- Configured Telegram as primary messaging interface
- Configured iMessage via BlueBubbles with dedicated Apple ID for privacy boundary
- Set up local voice transcription via whisper.cpp (offline, no API calls)
- Integrated ElevenLabs TTS in tagged mode (on-demand only)
- Configured multi-provider LLM fallback chain: Claude Sonnet → GPT-4o → Gemini Flash → Haiku
- Set context auto-compaction at 60k → 40k tokens

### Bug Fixes & Contributions
- Diagnosed TDZ initialization bug in OpenClaw (`ANTHROPIC_MODEL_ALIASES`) causing silent TTS/config failures
- Filed [Issue #45540](https://github.com/openclaw/openclaw/issues/45540) — fix merged within 24 hours
- Filed [Issue #45552](https://github.com/openclaw/openclaw/issues/45552) — feature request for `openclaw doctor --check-config`
- Diagnosed kernel panic root cause: DisplayLink 15.1.0 + node CPU spike interaction on Intel Mac
- Applied `pmset` mitigations; identified compatible DisplayLink 15.0 for Tahoe 26

### Integrations
- Google Drive API — file upload, sharing, permissions management
- Google Docs API — live in-place document editing (insert, find/replace, delete, append)
- Gmail, Calendar, Sheets, Contacts — authorized via OAuth2 (ready for use)
- `gogcli` installed and authenticated via Google Cloud project "Chip Assistant"

### Automations
- Weekly grant opportunity scanner (Monday 9am PT) — delivers to Telegram + appends to Google Doc
- Living shared Google Doc with editor access for collaborators

### Real-World Deployment
- Deployed for nonprofit grant operations: built grant writing reference, prospect list, and shared strategy document
- Configured weekly cron job for ongoing grant research

### Repository
- Created `judeglenn/ai-agent-homelab` on GitHub
- Published README with full architecture overview, stack, and notable contributions

---

## [2026-03-17] — Projects, Integrations & Stability

### New Projects Built
- **[Jamf App Inventory](https://github.com/judeglenn/orchardpatch)** — Next.js 14 + TypeScript + Tailwind dashboard for centralized macOS fleet software inventory. Features app catalog, version conflict detection, device drill-down, Jamf Pro API-ready architecture with mock data mode. Styled to match Jamf Pro aesthetic.
- **[Chip Mission Control](https://github.com/judeglenn/chip-mission-control)** — Native macOS SwiftUI menubar app for monitoring OpenClaw gateway health, cron jobs, active projects, and quick-launch shortcuts. No dock icon, auto-refreshes every 30s.

### Infrastructure Improvements
- **Gateway LaunchAgent** — Installed as macOS LaunchAgent with `KeepAlive=true` and `RunAtLoad=true`. Auto-restarts on crash, survives reboots.
- **Gateway port** — Changed from default 18789 to randomized 62314 for security
- **OpenAI fallback fix** — Corrected `baseUrl` to include `/v1` path, resolving 404 errors during Anthropic rate limit fallback

### New Integrations
- **Google Drive + Docs API** — Full OAuth2 integration via `gogcli`. Can read, write, find/replace, and append to live Google Docs
- **QM Grant Strategy doc** — Live shared Google Doc with Queer Mountaineers leadership team, updated automatically
- **Weekly grant scanner** — Monday 9am cron job searches for new grant opportunities and appends to shared doc

### Queer Mountaineers Grant Work
- Built comprehensive grant prospect database with deadlines and application angles
- Identified Social Justice Fund NW (May 7 deadline) as most urgent opportunity
- Submitted Jamf Pro trial application for jamf-app-inventory testing

---

## [2026-03-18] — OrchardPatch Founded

### New Venture: OrchardPatch (orchardpatch.com)
- Identified market gap: no clean MDM-agnostic fleet app visibility + patch tool that works alongside Jamf without conflicts
- Researched competitive landscape: 76,500+ Jamf customers, $70M–$300M SAM, 18–24 month window before Jamf builds it themselves
- Named the product: OrchardPatch (an orchard = a managed collection of Apple trees)
- Registered orchardpatch.com
- Built full waitlist landing page (Next.js + Tailwind) in an afternoon
- Generated logo using Nano Banana Pro (Gemini image generation)
- Deployed live to Vercel with custom domain: https://orchardpatch.com
- Core positioning: visibility-first fleet management layer that works alongside any MDM
