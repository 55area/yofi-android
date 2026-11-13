# Yo-Fi Android development and evaluation strategy

The pilot will deliver a usable Android app with the Teknolista skills and spec-driven development (SDD) using OpenSpec. We will record how each material increment reached a verified result so the next project can reuse the lessons.

## Start with the first journey

1. Review the [product concept](Yo-Fi_5PRO.md), [Android notes](Yo-Fi_android.md), and [idea archive](Yo-Fi_ideia.md). Confirm the first user, problem, and task they need to complete. Confirm boundaries that affect the journey, such as whether the app must connect to a network or can start with discovery using synthetic data. Treat the existing product and workflow proposals as discovery inputs. Clarify the scope of prior proposals about prompt retention, coverage thresholds, and multi-agent governance before adopting them as project rules.
2. Inspect the Android workspace, SDK, build tooling, and available test targets. Set up a reproducible Android build and the project's OpenSpec workflow as part of the authorized setup. Record the chosen OpenSpec version, profile, and resolved root. Use a fixed source snapshot for the installed Teknolista skills. If the snapshot comes from a working tree, preserve the package bytes and a manifest or content hashes in a reproducible location, since the base Git commit alone describes only committed files. Record the initial environment and installation in the [pilot log](DEVELOPMENT-LOG.md).
3. Capture the product context, architectural constraints, verification plan, operating needs, and process with PentaPRO. Link this context to the project's accepted OpenSpec change. OpenSpec owns the increment's behavior, acceptance scenarios, and tasks. Product context and project-wide decisions stay in their owning documents. Agree on the first complete, observable journey before implementing it.

## Work through each change

1. **Propose and confirm.** Record the outcome, scope, affected parties, and success and failure scenarios in the OpenSpec change. The product owner confirms consequential product decisions. The agent proposes tradeoffs and handles routine implementation choices within the agreed scope.
2. **Implement and verify.** Use the smallest set of skills relevant to the task. Cover the affected boundary and meaningful failure conditions. For example, a data migration may need a preserved database from the prior version with populated rows. A connection journey may need a representative Android device and controlled network conditions. Distinguish evidence from JVM tests, emulators, physical devices, and service environments.
3. **Review and close.** Use an independent implementation or security review when consequence warrants it and the review environment supports it. Link the reviewed revision and findings. Check the accepted scenarios, actual execution results, uncovered conditions, and completion of the existing tasks. Validate the OpenSpec artifacts separately from runtime checks. When the project authorizes archiving, inspect the merged behavior and strictly validate the resulting capability specification. Capture the next action and the assessment in the pilot log.
4. **Inspect the process.** After the first journey and at meaningful milestones, check where verification or handoff needed unexpected work. Correct project-specific commands or assumptions in their owning records. If a skill changes during the pilot, preserve the prior snapshot and record why and where the new snapshot started.

## Choose skills by the task

| Task | Starting point |
| --- | --- |
| Clarify a product decision or create or review global specification context | `penta-pro`. Link the relevant OpenSpec requirement rather than repeating it. |
| Coordinate an accepted increment across dependent areas | `software-delivery` with the accepted change. |
| Design a screen or interaction | `ui-design`. A tested native interaction can also need `android-ui-testing`. |
| Implement Android module, storage, error handling, DI, or durable work changes | The corresponding Android implementation skill when its technology and boundary apply. |
| Author native checks | `android-unit-testing`, `android-integration-testing`, or `android-ui-testing` by the boundary to verify. |
| Investigate a hard-to-reproduce failure or unresolved technical question | `diagnose-defect` or `technical-research` according to the task. |
| Independently review implementation, assess threats, or verify security controls | `peer-review` or `cybersec` according to the scope. |
| Deliver a scoped Git change or prepare an authorized release | `git-delivery` for Git actions. Use the applicable Android build, device, and distribution workflow for the app. Use `production-operations` if a hosted workload is in scope. |

A clear local edit can follow the existing contract and use a focused check. For exploratory work, document the question and use a disposable prototype with synthetic inputs. If the prototype becomes part of Yo-Fi, agree on its acceptance criteria and verify the maintained implementation.

## What to observe

Use the [pilot log](DEVELOPMENT-LOG.md) for workflow evidence. Keep product checks and results in the project's verification record, such as PentaPRO's PROOF document once adopted. Link the relevant command, tested revision, variant, device or service, outcome, and remaining coverage. Record decisions, prompts that exposed a problem, and meaningful handoffs as concise sanitized excerpts when useful. An installed or named skill establishes availability or intent. Note observed selection, consultation, application, and measurable effects separately.

| Question | Evidence to compare |
| --- | --- |
| Did the agent reach the intended result? | Accepted scenarios, behavior at the actual boundary, regressions, escaped defects, evidence gaps, and review findings. |
| Did it choose and apply appropriate guidance? | Applicable opportunities, unnecessary activation, missed selection, observed use, instructions skipped or misapplied, and instructions followed that still led to a poor result. |
| What did the workflow cost? | Elapsed and hands-on time when available, interventions, reruns, rework, interruptions, and token usage if reported. Compare similar tasks and record material differences in tools or scope. |

## End-of-pilot assessment

Summarize the observed results and limits after the app's agreed scope is delivered and verified. For relevant skills, distinguish inapplicable work, unavailable tools, unknown selection, missed selection, unnecessary use, successful application, skipped guidance, and guidance that produced a poor outcome. Use the number of applicable opportunities as the denominator. A skill unused because its task never arose remains unevaluated by this pilot.

For a proposed change to a reusable skill, take a sanitized task from the app and preserve its initial revision, prompt, success criteria, and a positive or negative control where useful. In disposable copies, compare the used snapshot with the candidate under matched model, tools, permissions, and success criteria. Add a run without the skill when it answers a specific question about its contribution. Keep evaluation-only expectations outside the agent's context. Check correctness, security, and scope before comparing time or effort. A single project or trial offers limited evidence. Carry a change forward when the result is repeatable on a meaningful task with acceptable effort.

The final retrospective should link to evidence and disposition each proposed improvement. Put project-specific facts in Yo-Fi's instructions, objectively enforceable behavior in tests or CI, and reusable guidance in the corresponding Teknolista skill following review in that repository.

## How this adapts the collection records

| Collection file | Application in Yo-Fi |
| --- | --- |
| `SKILL-ROUTING.md` | Use the task table above and the entrypoints from the recorded snapshot. |
| `SKILL-EVALUATION.md` | Apply the evaluation method above to real Yo-Fi work and focused replays. Keep the collection's synthetic fixtures with the collection. |
| `SKILL-ASSESSMENT.md` | Record initial versions and assumptions in the pilot log. Assess skill behavior after applicable work. |
| `REVIEW-REPORT.md` | Record observed issues and review evidence as they arise. Prioritize validated findings at milestones. |
| `COLLECTION-REVIEW.md` | Track each improvement's disposition in the pilot log and hand reusable proposals to the collection. |
| `FINAL-SKILL-REVIEW.md` | Write the final Yo-Fi retrospective at closeout, once there are results to assess. |

See the [Teknolista collection](https://github.com/teknolista/skills) for installation and its consolidated development guide. Use the PDF from the selected snapshot when running the pilot.
