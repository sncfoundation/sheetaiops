<p align="center">
  <img src="https://sncfoundation.github.io/logos/sheetaiops.svg" width="96" alt="SheetAIOps logo">
</p>

<h1 align="center">SheetAIOps</h1>

<p align="center"><b>Anomaly detection over the Events tab</b><br>
A <a href="https://sncfoundation.github.io">Sheet-Native Computing Foundation</a> project &#183; analog of <b>AIOps</b></p>

---

**Status:** 💾 Autosaving &#183; promoted from Unsaved Draft. Working code, released as [v0.1.0](https://github.com/sncfoundation/sheetaiops/releases/tag/v0.1.0), and the [tracking issue #23](https://github.com/sncfoundation/sheeternetes/issues/23) is closed. Google keeps saving it.

## About

Anomaly detection over the Events tab. Part of the spreadsheet-native stack — the Sheet stays the source of truth, and it reconciles.

`sheetaiops` reads events and nodes from the Sheeternetes apiserver, applies a
handful of simple, real heuristics, and prints a ranked incident summary:

- **restart storms** — the same object restarting repeatedly within a time window,
- **node health** — nodes reporting `NotReady`, plus Ready nodes whose heartbeat has gone stale,
- **error signals** — crash / OOM / panic / fail keywords in event messages.

Each incident gets a severity score (`CRITICAL` / `HIGH` / `WARN` / `INFO`) and the
list is ranked so the worst thing is line 1. With `--llm` it can also POST the summary
to the Anthropic Messages API for a natural-language root-cause guess.

## Usage

```bash
# full incident report (reads WEBAPP_URL/TOKEN, like skctl)
./sheetaiops

# tune the heuristics
./sheetaiops --window 600     # restart-window in seconds (default 600)
./sheetaiops --restarts 3     # restarts within the window to flag (default 3)
./sheetaiops --stale 180      # stale-heartbeat threshold in seconds (default 180)

# machine-readable incidents
./sheetaiops --json

# add an LLM root-cause guess (no-op with a clean message if the key is unset)
export ANTHROPIC_API_KEY=sk-ant-...
./sheetaiops --llm
```

### Configuration

Same as `skctl` — via environment or a `.skctl.env` file next to the script:

```bash
WEBAPP_URL=https://script.google.com/macros/s/XXXX/exec
TOKEN=CHANGE_ME_super_secret
```

Optional for `--llm`: `ANTHROPIC_API_KEY` (and `ANTHROPIC_MODEL`, default
`claude-3-5-haiku-latest`).

### Offline smoke-testing

To exercise the heuristics without a live apiserver, feed the payloads directly.
The apiserver is never contacted when `SHEET_EVENTS_JSON` is set:

```bash
export SHEET_EVENTS_JSON='{"items":[
  {"ts":"2026-08-31T15:01:00Z","kind":"Pod","object":"web-abc","message":"container exited -> restarting"},
  {"ts":"2026-08-31T15:02:00Z","kind":"Pod","object":"web-abc","message":"container exited -> restarting"},
  {"ts":"2026-08-31T15:03:00Z","kind":"Pod","object":"web-abc","message":"container exited -> restarting"}
]}'
export SHEET_NODES_JSON='{"items":[{"name":"node-b","status":"NotReady","last_heartbeat":"2026-08-31T14:40:00Z"}]}'
./sheetaiops
```

**Requires:** `bash`, `curl`, `jq`. Optional for `--llm`: `ANTHROPIC_API_KEY`.

## Get involved

- 📋 Tracking issue &amp; design: [sncfoundation/sheeternetes#23](https://github.com/sncfoundation/sheeternetes/issues/23) (closed)
- 🗺️ [SNCF Landscape](https://sncfoundation.github.io/landscape.html)
- 🧩 [All projects](https://sncfoundation.github.io/projects.html)
- ⚖️ [Governance &amp; how to contribute](https://github.com/sncfoundation/governance)
- 🎓 [Get certified (CSFE)](https://sncfoundation.github.io/certification.html)

## Status legend

Everything starts as an **Unsaved Draft**. It reconciles up the tiers from there — see the
[maturity model](https://sncfoundation.github.io/foundation.html#maturity).

---

<sub>Licensed under Apache-2.0. The SNCF does not recommend running production on a spreadsheet. If you do, please film it.</sub>
