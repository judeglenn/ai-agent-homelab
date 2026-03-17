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
