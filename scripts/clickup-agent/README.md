# Clickup Agent — ClickUp Project Agent (operations)

Watches ClickUp list `1300430000066500` and reacts to task activity.
Event-driven — no cron, no servers to maintain. Installed by
[ai-automations](https://github.com/makasanaakshay/ai-automation-master);
re-run its installer to update (answers saved in `automation.config.json`).

```
ClickUp webhook (HMAC-SHA256 verified via X-Signature)
  → Cloudflare Worker relay            scripts/clickup-agent/relay/worker.js
  → repository_dispatch                .github/workflows/clickup-agent.yml
  → agent-run.sh                       restore state → resolve → playbook → save state
```

| Trigger | Playbook | Output (one ClickUp comment) |
|---|---|---|
| Task enters **To Do** | `analyze` | Options + trade-offs + recommendation, or numbered questions |
| Human comment on a To Do task | `respond` | Answer / updated proposal / remaining questions |
| Task enters **IN PROGRESS** (or comment there) | `implement` | PR into `main` — or questions if unclear |

State lives on the orphan branch `clickup-agent-state` (`state/<card_id>.json`:
list_id, phase, last_comment_date, branch, pr_url). Every run re-fetches truth
from ClickUp's API and diffs against it — webhook payloads are only
doorbells. Agent comments are prefixed `🤖 Clickup Agent — `; the resolver and
relay both skip marker comments (loop protection). Comment recency is tracked
by the comment `date` field (ms epoch).

## Operating

- **Recover / backfill:** Actions → *ClickUp Project Agent* → *Run workflow* — empty `item_id` reconciles both watched statuses + tracked tasks; a task id processes one.
- **Transcripts:** every run uploads `.agent-out/` as an artifact.
- **Model:** `AI_MODEL` in `scripts/clickup-agent/env.sh` (currently `claude-sonnet-5`, brain `claude-code`).
- **Pause:** `api.sh DELETE "/webhook/<webhook_id>"` (list them: `api.sh GET "/team/1300430000010578/webhook"` — also shows webhook health).
- **Known blind spot:** moving a task back into a watched status silently — re-trigger with a comment or a manual run.

## Local dry-run (safe — needs CLICKUP_TOKEN exported)

```bash
DRY_RUN=1 bash scripts/clickup-agent/agent-run.sh                 # reconcile scan
DRY_RUN=1 ITEM_ID=<task_id> bash scripts/clickup-agent/agent-run.sh
```

## Guardrails

- ClickUp writes are **comments only** — the agent never changes statuses, assignees, or anything else; humans own the board.
- PRs are **always against `main`** (branch `ai-agent/<kebab-slug>`); the agent never merges and never pushes to `main` directly.
- `--dangerously-skip-permissions` only inside the disposable CI runner; local runs are `DRY_RUN` and stop before any AI call.
