# imkodying — Workspace Conventions

Read this document in full before beginning any work.

---

## The Workspace Directory

All inter-agent file passing uses a directory called `.imkodying/` created in the **current working directory** (wherever Claude Code is running). This directory is created fresh for each orchestration run.

```
[current project]/
└── .imkodying/
    ├── planner.md          ← written by planner (Phase 1)
    ├── researcher.md       ← written by researcher (Phase 1)
    ├── implementer.md      ← written by implementer (Phase 1)
    ├── tester.md           ← written by tester (Phase 1)
    ├── review-pack.md      ← written by orchestrator (Phase 2 setup)
    ├── review-R1.md        ← written by reviewer R1 (Phase 2)
    ├── review-R2.md        ← written by reviewer R2 (Phase 2)
    ├── review-R3.md        ← written by reviewer R3 (Phase 2)
    └── review-R4.md        ← written by reviewer R4 (Phase 2)
```

---

## Writing Your Output

### Specialists (Phase 1)

You will be told the exact file path to write your output to in your task instructions. It will be one of:
- `./.imkodying/planner.md`
- `./.imkodying/researcher.md`
- `./.imkodying/implementer.md`
- `./.imkodying/tester.md`

Write your **complete, final output** to this path using the Write tool. Do not write partial drafts or stubs. The file must be complete before you finish — the orchestrator will read it directly.

Use this structure at minimum:
```markdown
# [Your Role] Output — [brief task description]

[your full output here, organized with headers]
```

### Reviewers (Phase 2)

You will be told to write your review to one of:
- `./.imkodying/review-R1.md`
- `./.imkodying/review-R2.md`
- `./.imkodying/review-R3.md`
- `./.imkodying/review-R4.md`

Write your complete review before finishing.

---

## File Handling Rules

- **Always use the Write tool**, not shell commands, to create output files
- **Do not read other specialists' output files** during Phase 1 — that breaks independence
- **Do not modify files written by other agents** — each agent owns exactly one output file
- **Check that `.imkodying/` exists** before writing; if it does not, create it first with the Bash tool: `mkdir -p ./.imkodying`
- **Overwrite is acceptable**: if you need to revise your output, simply write the file again with the complete updated content

---

## What Happens to Your Output

Your output file is read by:
1. The **orchestrator**, who anonymizes it and includes it in the review pack
2. **Four reviewers**, who read the anonymized version and score it

Your file contents will be shown verbatim to reviewers — format clearly. Your name and role will not be revealed to reviewers.

---

## Workspace Lifetime

The `.imkodying/` directory persists after the run. It is safe to delete. The orchestrator does not clean it up automatically. If a new run starts on the same task, files will be overwritten.

---

## Plugin Files (Read-Only Reference)

These files are part of the plugin and are never modified during a run:

| Path | Purpose |
|------|---------|
| `/Users/kody/imkodying/core/SYSTEM.md` | System overview |
| `/Users/kody/imkodying/core/WORKSPACE.md` | This file |
| `/Users/kody/imkodying/core/OUTPUT_STANDARDS.md` | Quality standards |
| `/Users/kody/imkodying/core/AGENT_CONTRACT.md` | Agent rules |
| `/Users/kody/imkodying/library/INDEX.md` | Library catalog |
| `/Users/kody/imkodying/library/*.md` | Reference documents |

Do not write to any file under `/Users/kody/imkodying/`. Plugin files are read-only reference material.
