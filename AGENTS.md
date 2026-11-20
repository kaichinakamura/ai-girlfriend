# Codex project instructions — AI彼女 第0話

## Source of truth
- Read `spec.md` from beginning to end before planning or editing.
- `spec.md` is the authoritative product/technical specification. This file supplements it; if anything here conflicts with `spec.md`, follow `spec.md` and report the discrepancy.
- This is a **Mac-local-first, Japanese, half-duplex voice conversation MVP**, not a cloud-first app. Preserve the interfaces and milestones in the specification.
- The end goal is a reproducible, MIT-licensed **application source repository**; licenses of third-party programs, model weights, and synthesized voices remain separate.

## First action
1. Inspect `spec.md`, the repository, host OS/CPU architecture, RAM, disk availability, available package managers and runtimes, and any existing project settings.
2. Build a checklist mapping each `spec.md` requirement and milestone (M0–M6 where applicable) to an implementation task, test, or explicitly identified manual step.
3. Identify concrete external dependencies and models **by real, verified upstream project/model IDs**, installation mechanism, license/terms link, and runtime footprint. Do not invent repositories, model names, voice IDs, endpoints, or license permissions.
4. Start implementation after the assessment, proceeding milestone-by-milestone. If a step is blocked by a human-only permission, subscription, user choice, or hardware issue, implement the other feasible parts and document the exact blocker.

## Defaults and boundaries
- Keep STT / LLM / TTS / VAD modules replaceable and configured rather than hard-coded.
- Prefer approved, maintained upstream package managers, official apps, and documented model APIs over copying or vendoring whole upstream repositories.
- Models may download automatically into the **user/system cache outside Git** on the first run, or via an explicit `scripts/setup_models.*` command. Document what is downloaded, its source, size, and expected network access. Do not check in model weights, audio recordings, API credentials, user data, or caches.
- The **local LLM** is the default. Groq is an optional, explicitly enabled comparison mode; simply providing an API key must **not** silently switch to it.
- Never send local conversation history, recordings, transcripts, or personal information to a remote provider without an explicit user action and clearly shown scope. Do not silently carry over local history when entering Groq mode.
- Use `.env` or secure local environment configuration for secrets, and commit only a sanitized `.env.example`. Ensure `.env` is gitignored.
- Use a currently available, user-selectable Groq model ID rather than assuming an old ID remains supported; validate failures and rate limits.
- Do not require `sudo`, change machine-wide settings, install background agents, expose services beyond `127.0.0.1`, or execute destructive commands without the user's explicit approval.
- On non-Apple-Silicon hardware, do not pretend MLX works. Report the mismatch and suggest a documented alternative without silently violating the specification.
- Mac microphone permission, audio-device selection, login/API-key creation, platform license acceptance, and editorial approval of a voice's publication/monetization rights remain human checkpoints.

## YouTube / distribution / voice licensing
- Never treat an MIT code license as permission to redistribute third-party models or to use any particular voice commercially.
- Before selecting default TTS voice, distinguish **engine/source license**, **voice model license**, **character/persona/voice-provider terms**, **required attribution**, and **rights for generated audio**. Document evidence and unresolved conditions in `THIRD_PARTY_NOTICES.md` or `docs/licenses.md`.
- The first TTS implementation may connect to an independently installed VOICEVOX-compatible local engine. It must discover speakers/styles via the engine API, not assume that a particular voice is available.
- Do not commit voice weights, packaged third-party voices, or characters/portraits unless their redistribution is explicitly authorized.
- Do not assume future **voice conversion, retraining, or singing** is licensed merely because generated speech can be published.

## Engineering and acceptance
- Implement the specified UI and half-duplex flow with explicit states, start/stop, clear errors, and cancellation; do not record while playback is in progress if the specification prohibits it.
- Add automated tests with fake/mocked audio and APIs so most checks work without a microphone, TTS engine, Ollama process, or Groq API key.
- Provide a local diagnostics / preflight command and actionable messages for absent models/services, wrong hardware, permission issues, and incompatible speaker selection.
- Generate README quickstart steps for first-time Mac setup; document Python version, dependencies, how to install/launch the engine, initial model download, offline usage, and how to run tests.
- Include `.env.example` matching actual implementation variables (without secrets).
- Run static analysis/unit tests and any available integration checks. Never claim that a real speaker/microphone test passed unless it actually ran.
- Preserve incremental commits if appropriate, and give a final summary: implemented milestones, checks actually run, model downloads completed, remaining human-only steps, license blockers, and exact launch instructions.
