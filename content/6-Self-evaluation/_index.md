---
title: "Self-evaluation"
date: 2026-11-25
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

During the First Cloud AI Journey internship I was the AI member (AI1) of the team project **Ask First**. I worked on the scam-check pipeline: choosing and measuring OCR, choosing the Bedrock judge model, building the eval set and the level thresholds, packaging the `ai` Lambda as a container image, and measuring latency and cost on AWS. The table below is my self-evaluation; the evidence comes from my worklog and the project repo.

| No. | Criterion | Good | Fair | Average | Comment / evidence |
|---|---|---|---|---|---|
| 1 | Professional knowledge | ✅ | | | Worked hands-on with Lambda container images, SQS, DynamoDB, Bedrock (Qwen3 32B, Guardrails), OCR and model evaluation. Each technical decision has an ADR with measurements (ADR-001, ADR-003, ADR-005). |
| 2 | Self-learning | ✅ | | | Learned Vietnamese OCR, prompting an LLM judge, measuring recall and false alarms, and packaging Lambda containers; most of this was new to me before the program. |
| 3 | Proactiveness | ✅ | | | Reran benchmarks on CI to check the dev-machine numbers, built a separate red-team set (34 samples), and wrote down the limits myself (134 samples instead of 300, no real samples yet). |
| 4 | Discipline | | ✅ | | Most work met its milestone, but gate G1 (choose the LLM) slipped from 30/10 to 11/11. PRs were often merged in bursts and the worklog was not kept daily. |
| 5 | Communication | | ✅ | | Mostly through issues, PRs and ADRs, with the BE ↔ AI contract v1.0 so both sides could work in parallel. I could flag schedule risks to the team earlier. |
| 6 | Teamwork | ✅ | | | Reviewed about 26 PRs from AI2 and BE in weeks 6–10; the contract ships a `StubPipeline` and fixtures so BE could write Java without waiting for the AI pipeline. |
| 7 | Problem solving | ✅ | | | Switched URL scoring from substring to boundary matching (#105); kept separate thresholds for the rules-only path when the new ones failed the CI eval gate; handled the `ai` Lambda's 10 s INIT limit with a post-deploy warm-up (#185). |
| 8 | Contribution to the Workshop | ✅ | | | The AI pipeline, eval report, Lambda latency numbers and cost estimate (about 0.000753 USD in tokens per text check) are used directly in the Proposal and Workshop. |

### Areas to improve
* **Set criteria before measuring.** The OCR CER ≤ 10% threshold was set after seeing the results. Next time I fix thresholds and scoring rules before running.
* **Keep a steadier pace.** Write the worklog daily, keep PRs small and merge them steadily, and raise a flag early when a gate may slip.
* **Thin evaluation data.** The eval set has only 134 samples and no real ones, and image end-to-end p95 is 17.0 s, above the 15 s target. These are the two things I would work on next.
