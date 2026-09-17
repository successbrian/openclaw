# STRIP-DOWN — SuccessBrian OS agent runtime

This fork of [OpenClaw](https://github.com/openclaw/openclaw) is being stripped
down to the minimal runtime needed by three agents:

- **Altair** — VP of Infrastructure / Chief Opportunity Scout (Hermes-based,
  uses OpenClaw gateway for orchestration).
- **Lyra** — headless OS / hardware watchdog (`lyra-successbrian-os`).
- **CEO agent (Meghan Alpha)** — the event-driven decision engine.

Everything not required by these three agents is removed. The goal is a small,
auditable runtime, not the full multi-channel gateway.

## Keep

### Core (packages/)
All 22 core packages — `agent-core`, `llm-core`, `model-catalog-core`,
`plugin-sdk`, `plugin-package-contract`, `sdk`, `gateway-*`, `ai`, `retry`,
`terminal-core`, `tool-call-repair`, `markdown-core`, `normalization-core`,
`memory-host-sdk`, `net-policy`, `workboard-contract`, `acp-core`,
`session-url-contract`, `mermaid-renderer`, `media-*`.

### Native (crates/)
`openclaw-gateway-client`, `openclaw-node-host` (node-sqlite + host bindings).

### Model providers (extensions/)
Only what the agents actually use (no Ollama — we're off local models):

- `deepseek` — primary reasoning model (`deepseek-reasoner`)
- `kilocode` — free-model gateway (minimax/laguna fallbacks)

### Plugins / tools (extensions/)
Only the 4 in `openclaw.json` `plugins.allow` that we actually use:

- `searxng`, `duckduckgo`, `deepseek`, `memory-core`

### Channels (extensions/)
- `telegram` — kept (Brian's primary channel), though currently disabled.

### CLI / entry
- `openclaw.mjs`, `package.json`, `pnpm-workspace.yaml`, `config/`, `scripts/`.

## Strip

- **apps/** — `android`, `ios`, `linux`, `macos`, `macos-mlx-tts`, `mobile`,
  `shared`, `swabble` (all client apps — not needed for headless agents).
- **extensions/** — ~145 unused: every other model provider (anthropic, openai,
  mistral, groq, xai, gemini, vertex, litellm, vllm, lmstudio, …), every other
  channel (discord, slack, whatsapp, signal, imessage, matrix, msteams, sms,
  irc, …), and all media/voice/tts/music/3d/document/coding integrations.
- **skills/** — all 52 (consumer/integration skills: 1password, notion, obsidian,
  spotify, apple-*, himalaya, …). None are used by the three agents.
- **custodian-skills/** — TBD (keep `diagnose-gateway` if used; drop the rest).
- **examples/** — remove (docs/examples not needed in the runtime).
- **apps/shared**, **qa/**, **deploy/** — TBD after a boot smoke-test.

## Execution order

1. Fork + clone (done).
2. Boot-smoke-test the full fork to establish a working baseline.
3. Remove `apps/`, `skills/`, unused `extensions/` in batches, updating
   `pnpm-workspace.yaml` + `tsconfig` paths + `package.json` after each batch.
4. Re-run the smoke test + `pnpm build` after each batch to keep the gateway
   bootable.
5. Push to `successbrian/openclaw` on a `stripped` branch; keep `main` tracking
   upstream for sync.

## Status

- [x] Fork `successbrian/openclaw` (PUBLIC)
- [x] Clone + structural map
- [ ] Baseline boot smoke-test
- [ ] Strip apps/ + skills/ + unused extensions/
- [ ] Build + boot verification after strip
- [ ] Publish stripped runtime (README + positioning)
