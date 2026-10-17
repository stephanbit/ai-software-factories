# Software factory

A spec → build → review → approve loop for Claude Code, adapted from
[zazencodes/software-factory-claude-code](https://github.com/zazencodes/zazencodes-season-3/tree/main/src/software-factory-claude-code).

```
feature → spec-writer → builder → [security, ux, ui, code] → WAIT FOR ALL
                           ▲                                      │
                           └──── any CHANGES: resume builder ◄────┤
                                                                  ▼ all PASS
                                  ready for PR ◄─ APPROVE ─ approver ─ ESCALATE → needs-human
```

The session that runs `/factory` orchestrates. Each job gets its own worktree
(`../webapp-wiedervorlage-factory/<job-id>/`) on a `factory/<job-id>` branch
cut from `origin/master`, so several sessions can run jobs in parallel. Every
agent writes one markdown file into `factory/jobs/<job-id>/` (gitignored).

The factory never merges and never pushes. An approved job stops at
**Ready for PR**: push the branch, rename it to `feature/NDMS-<n>-<slug>` if the
PR needs that, and open the PR in Bitbucket.

## Commands

```
/factory <feature>               run a new job through the loop
/factory next                    run the first unchecked item in factory/backlog.md
/factory approve <job-id>        accept an escalated job as ready for a PR
/factory rework <job-id> <note>  send a needs-human job back to the builder
/factory done <job-id>           remove the job's worktree and branch after its PR merged
```

## Dashboard

```bash
cd factory && python3 -m http.server
```

Open <http://localhost:8000/dashboard.html>. It polls the job files every two
seconds.

## Requirements

- `CLAUDE.md` at the repo root: the agents read setup, dev server, validation
  commands and conventions from it and nothing else.
- The [`agent-browser`](https://github.com/vercel-labs/agent-browser) CLI, which
  the builder and the UX/UI reviewers use to look at the running app. Pages
  behind login stay out of reach, because agents never sign in.

## Changing it

The loop lives in `.claude/skills/factory/SKILL.md`, the agents in
`.claude/agents/`. Escalation rules for this repo are in `approver.md`.
