---
title: "Week 5 Worklog"
date: 2026-10-12
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

Week 5: 12/10/2026 – 16/10/2026. The Sun row is Sunday 18/10/2026, when the project repo got its first commit.
<!-- draft: filled in from the usual FCAJ schedule; the project repo has no evidence for this week (first commit 18/10/2026); correct it to match what you actually did before submitting -->

### Week 5 objectives

* Write the research concept, architecture and first implementation plan.
* Write and test the BE ↔ AI data contract so both sides can work in parallel.
* Set up the project repo and create the first tasks.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Wrote the research concept: problem, users, scam scenarios, success criteria | 12/10/2026 | 12/10/2026 | [research-concept.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/research-concept.md) |
| 3 | - Drew the architecture: Telegram → `webhook` Lambda → SQS → `worker` → `ai` Lambda → Bedrock; chose not to use a VPC | 13/10/2026 | 13/10/2026 | [system-architecture.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/system-architecture.md) |
| 4 | - Wrote the first implementation plan (12 weeks, gates G0–G2, milestones M1–M6) | 14/10/2026 | 14/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| 5 | - Wrote sample Python code for the BE ↔ AI contract (pydantic) and ran 20 tests and `ruff` <br> - Wrote research notes on local AI (OCR, LLM), which later became 3 side research tasks for AI2 | 15/10/2026 | 15/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) (§3), [ai-viec-nghien-cuu-phu.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/ai-viec-nghien-cuu-phu.md) |
| 6 | - Wrote the guides for the BE and AI teams | 16/10/2026 | 16/10/2026 | [huong-dan-backend.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/huong-dan-backend.md), [huong-dan-ai.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/huong-dan-ai.md) |
| Sun | - Made the first commits of the repo: skeleton, CI, CODEOWNERS, architecture docs, BE/AI guides, architecture image <br> - Opened issues #1–#18 (tasks BE-xx, SH-xx, AI-xx) | 18/10/2026 | 18/10/2026 | commit [4842bd3](https://github.com/ask-first-fcaj/ask-first/commit/4842bd3), [c1ef4b6](https://github.com/ask-first-fcaj/ask-first/commit/c1ef4b6), [issues](https://github.com/ask-first-fcaj/ask-first/issues?q=is%3Aissue) |

### Achievements

* The first set of docs went into the repo on 18/10: research concept, architecture, plan and two guides (commit [4842bd3](https://github.com/ask-first-fcaj/ask-first/commit/4842bd3), 4,569 lines).
* The Python code of the BE ↔ AI contract was test-run on 15/10 (20 tests pass) before handing it to BE.

### Lessons learned

* Sample code in a doc must be run before handing it over, with a clear note of what has not run: the plan states that the Java codegen had not run yet, so BE could check it in the first week.
