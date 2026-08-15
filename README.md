# demo-app

The target application for **The Agent Org** — a multi-agent CI/CD pipeline that
plans, writes, reviews, security-scans, and promotes a change without a human
writing the code.

This repo is deliberately tiny. It exists so the pipeline has a real file to
modify, a real diff to review, and real content for the scanners to scan.

## Layout

| Path | What it is |
|---|---|
| `app/auth.py` | Minimal Flask login handler — the file the agents modify |
| `tests/test_auth.py` | One small test so CI has something to run |

## How the pipeline uses this repo

The developer agent produces a unified diff against `app/auth.py`. The pipeline
then, via `agentorg/github_ops.py`:

1. branches off `main` as `agent-org/<ticket-id>-<short-sha>`,
2. commits the diff to `changes/<ticket-id>.diff`,
3. opens a pull request,
4. posts the security verdict back as a PR comment.

Two tickets drive the demo:

- **clean** — adds a per-IP login rate limit. Scanners pass, the run is promoted.
- **poisoned** — the same feature, but with hardcoded AWS credentials. Two
  critical findings, and the run is **blocked every single time**.

The block is not a judgement call: it is computed in pure Python by
`compute_security_verdict()`, so no model can talk the pipeline out of it.

## Running the app's tests

```bash
pip install -r requirements.txt
pytest -q
```
