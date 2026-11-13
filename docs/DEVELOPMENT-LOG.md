# Yo-Fi Android pilot log

Use the [development strategy](DEVELOPMENT-STRATEGY.md) for workflow and evaluation decisions. Add one entry per material OpenSpec change. Update it as work advances or pauses, and link raw evidence from its owning record. Leave unknown and unexecuted fields explicit.

## Monorepo setup

On 2026-11-13, the setup working tree based on `f2052030977711839c2f8240c02554c0116a9681` gained independent Android and landing page workspaces. This setup precedes the first accepted Android journey. The existing Portuguese discovery notes remain inputs to that discussion.

| Baseline item | Observed result |
| --- | --- |
| OpenSpec | CLI 2.0.0, `spec-driven` schema, repository root resolved from both subprojects. The local global profile is `custom` with propose, explore, apply, and archive selected. Five project workflows are installed, including sync specs. `openspec doctor --json` reported a healthy root and `openspec update --dry-run` reported current skills. A temporary change confirmed English context in generated proposal instructions. The repository has no active changes or capability specs yet. |
| Teknolista skills | All 22 packages match the tracked package contents in [Teknolista skills revision 6c25c8e](https://github.com/teknolista/skills/tree/6c25c8eb9dc86cfb55f4a18948759aa77b2492b3) byte for byte, including supporting files. The source collection validator passed all 22 version checks and 790 local link and anchor checks. The 27 installed skill entrypoints are visible in this Codex session and their metadata was checked. |
| Android | The [Android guide](../android/README.md) records the JDK, SDK, wrapper, and verification commands. The Gradle distribution checksum and wrapper JAR match the [Gradle 8.11.1 reference](https://gradle.org/release-checksums/). |
| Web | The [web guide](../www/README.md) records the HTML entry point and reserved source and asset directories. The starter HTML parsed successfully. Node.js tooling will be selected when landing page development begins. |

- **Skill use** — Codex consulted `agent-instructions` 1.1.1 for root and platform instruction ownership, `android-module-structure` 1.4.0 for the single native app module and toolchain checks, and `openai-docs` for Codex instruction discovery. The remaining skills have no behavioral evaluation from this increment.
- **Android evidence** — On Linux with JDK 21.0.5 and SDK Platform 36, `./gradlew --no-daemon --max-workers=2 -Pkotlin.compiler.execution.strategy=in-process :app:assembleDebug :app:lintDebug :app:testDebugUnitTest` succeeded. The build used an isolated Gradle cache and host access after the sandbox denied the Gradle socket and the offline cache lacked AndroidX versions. Lint reported zero errors and five warnings covering the API 33 locale attribute, the starter's missing launcher icon, and newer versions of three build dependencies. The unit test task reported `NO-SOURCE`. APK inspection confirmed the launchable activity, API 26 minimum, API 36 target, and packaged locales. `apksigner verify` passed. Debug APK SHA-256 was `60c030350a7060f21debeeb51e726fd43af0853e0759c896d8946d53f01efe5a`. Device execution remains unverified.
- **Next action** — Confirm the first Android journey and its acceptance criteria during discovery, then record the agreed scope in a root OpenSpec change.

## Android toolchain migration

On 2026-11-13, the setup working tree based on `f2052030977711839c2f8240c02554c0116a9681` moved to Gradle 9.9.1, Android Gradle Plugin 9.5.1, and Temurin JDK 25.0.4. AGP provides Kotlin 2.2.10, as confirmed by the Google Maven POM and the resolved compiler. The Compose compiler plugin uses the same version. The migration retains `minSdk = 26`, compile and target API 36, and Java and Kotlin JVM 17 targets.

- **Build evidence** — A temporary copy with the same build inputs and app sources passed `./gradlew --no-daemon --max-workers=2 -Pkotlin.compiler.execution.strategy=in-process :app:assembleDebug :app:lintDebug :app:testDebugUnitTest :app:assembleRelease`. The wrapper JAR and distribution matched the published SHA-256 checksums. The debug APK signature verified. APK metadata confirmed minimum API 26 and target API 36, and `javap` reported class version 61 for the launcher activity. The debug APK SHA-256 was `9c2aa8483780ff7f5815bfae725c3617510e73156d0ed39b9838ea6887db5bf6`. The release APK was assembled unsigned.
- **Check limits** — Debug lint reported zero errors and eight warnings covering dependency and SDK versions, a dependency lint check, the API 33 locale attribute, and the starter's missing launcher icon. Gradle also reported deprecations affecting Gradle 10. The unit test task reported `NO-SOURCE`.
- **Android 8 evidence** — On a disposable API 26 x86 AVD with KVM, ADB installed the debug APK and launched the main activity successfully. The UI hierarchy and screenshot showed “Yo-Fi” and “Community Wi-Fi sharing,” with no matching process crash in the crash buffer. The emulator initially refused to create a data partition because of free space. A clean copy of the SDK userdata image in the temporary AVD allowed it to boot.
- **Recent Android evidence** — The same APK passed the same install, launch, UI text, and crash-buffer checks on Android 15 (API 35, x86_64). The API 36 AVD failed to complete boot and reach an online ADB state across two attempts, so API 36 runtime and physical-device behavior remain unverified.
- **OpenSpec and skill use** — The root instructions now define when to use OpenSpec. A disposable change confirmed the proposal, specs, and tasks rules in CLI output, and the repository root passed `openspec doctor`. The work applied `agent-instructions` for configuration ownership, `android-module-structure` for version alignment and build verification, and `android-emulator-qa` for the launch and UI check.
- **Next action** — Confirm the first Android journey and its acceptance criteria in a root OpenSpec change.

## Entry template

Copy for the next material change.

### Change ID and short title

- **Scope and state** — Date, accepted change link, starting and current revision, current state, and next action.
- **Skill use** — Applicable opportunities, expected and observed skills, version, explicit invocation or automatic selection, and evidence of use. Use “unknown” when the trace is inconclusive. Include inappropriate selection or a useful case where guidance was missed.
- **Decisions and handoff** — Material decision or intervention, reason, and link to the owning record. Include a sanitized prompt excerpt only when it helps explain an outcome.
- **Product evidence** — Link to the verified revision, checks and findings. Note observed outcomes, gaps and failures discovered later.
- **Effort** — Time or usage when available, interventions, reruns, and work repeated and why.
- **Learning** — Observed issue or benefit, evidence, suspected cause, proposed disposition, and next check. Distinguish the contribution of the skill from project context, tooling, or agent behavior.

## Milestone review

At a milestone, review applicable opportunities and evidence from the entries. Track unresolved proposals with their owner and next check. After the agreed app scope closes, link the final retrospective here.
