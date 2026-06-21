# Governed Audio Learning Pipeline

Local-first pipeline that turns spoken content — Chinese or English voice recordings, local video files, or phone-generated `.txt`/`.md` transcripts — into governed learning artifacts:

- original-language transcript
- optional English markdown transcript
- quality-scored summary
- concept-map (mindmap) delta review
- sanitized, public-safe review package

The code repository is intended to be public. Raw audio, full transcripts, and private source material stay outside the repo. Inputs are limited to content the operator has the right to process — own voice notes, permitted talks and lectures, and self-generated transcripts.

## What it demonstrates

Mid-to-senior enterprise AI engineering practice in a small, operable system: private raw-data control, an OpenAI-centred quality layer, MCP-ready tools, explicit cost and quality gates, and human approval before anything is published.

## Operator quick start

1. Place this repo beside the private audio sandbox folder.
2. Copy `config/paths.local.example.yaml` to `config/paths.local.yaml` for machine-specific paths.
3. Keep FFmpeg portable or configured by path. Do not change the global PATH just for this app.
4. Drop audio/video or `.txt`/`.md` transcripts into the private sandbox `incoming/` folder.
5. Run a safe mock test first:

```powershell
python -m app process --input ".\tests\fixtures\sample.m4a" --mock
```

6. For a real run, configure FFmpeg, Whisper, and your OpenAI API key first. For frequent use on your own Windows account, store the key once as a user-level environment variable:

```powershell
.\scripts\set-openai-key-user.ps1
```

Then run:

```powershell
python -m app process --input "C:\path\to\recording.m4a"
```

To remove the saved key later:

```powershell
.\scripts\clear-openai-key-user.ps1
```

Core operator commands:

```powershell
.\run.ps1 tools --json
.\run.ps1 doctor --create-sandbox
.\run.ps1 process --input ".\tests\fixtures\sample.m4a" --mock
.\run.ps1 process-latest
.\run.ps1 quality --rescore
.\run.ps1 mindmap --review
.\run.ps1 publish --sanitize
.\run.ps1 exhibit --audit
```

## Architecture

```text
incoming audio/video/text transcript
  -> stable file check
  -> if media: ffmpeg normalize + Whisper transcribe + chunk/stitch when needed
  -> if text: read transcript directly
  -> detect English vs Chinese
  -> default: configured OpenAI quality model summarizes directly from source transcript in English
  -> optional: generate a full English transcript only when explicitly enabled
  -> mindmap ingest suggestion from the refined summary
  -> quality score + zero-cost local mindmap delta from summary-level text
  -> auto-apply passed deltas to the private learning graph
  -> private output package
  -> optional sanitized public-review package and public mindmap links
```

## Privacy model

- **Public:** app code, docs, architecture, safe examples.
- **Private:** raw audio, full transcripts, private source notes, local state.
- **Curated public:** sanitized summaries and reflections, after human review.

## Public exhibit gate

Before sharing any generated artifact, run:

```powershell
.\run.ps1 publish --sanitize
.\run.ps1 mindmap --review
.\run.ps1 exhibit --audit
```

`passed: true` means there are no public/privacy blockers. `portfolio_ready: true` means there are also no quality warnings. A review-safe draft can be useful internally, but it should not be presented as polished public learning until the audit is clean.

## Portfolio positioning

This project signals applied enterprise AI engineering judgment — not prompting, not platform theater, but a controlled pipeline with real governance:

- MCP-ready tool orchestration with a CLI fallback
- configurable OpenAI synthesis, with optional full-transcript translation
- local Whisper-compatible transcription
- agent contracts before agent work
- maker–checker separation
- manual-first autonomy
- hooks and quality gates
- ADRs and a build journal
- long-term memory as repo artifacts
- privacy-preserving publishing

The public story: **the operator designs and runs the pipeline; OpenAI and Whisper assist execution under human review, cost controls, quality gates, and privacy-preserving publishing.**

See `AGENTS.md`, `docs/real-audio-setup.md`, `docs/enterprise-readiness.md`, `docs/mcp-orchestration.md`, `docs/agent-contracts.md`, `docs/git-versioning.md`, and `docs/adr/`.
