# Yo-Fi project instructions

Yo-Fi is a monorepo with a native Android app in `android/` and a future promotional landing page in `www/`. Keep source, dependency files, local configuration, and build outputs in the owning subproject. Read the [Android instructions](android/AGENTS.md) or [landing page instructions](www/AGENTS.md) before editing that subproject, including when starting at the repository root. Keep shared rules here and add subproject instructions where platform-specific guidance is needed.

- Use English for repository content, including READMEs, documentation, specs, identifiers, comments, and commit messages. Localized app strings use their target language. Conversations with the product owner may be in Portuguese.
- Use the single repository-root [OpenSpec project](openspec/). State the affected subprojects in each change and keep requirements, acceptance scenarios, and tasks in their owning OpenSpec documents.
- Use the local contract and a focused check for routine, reversible edits. Use OpenSpec for material behavior changes and work through the tasks of the accepted change.
- Before drafting or implementing an increment, read the [development strategy](docs/DEVELOPMENT-STRATEGY.md). When resuming, find the accepted OpenSpec change and the latest entry in the [pilot log](docs/DEVELOPMENT-LOG.md).
- Use the existing Yo-Fi documents as discovery inputs and confirm consequential behavior with the product owner. Discover shared skills under [.agents/skills/](.agents/skills/) and use the ones relevant to the task.
- For a material change, trace implementation and checks to the accepted scenarios. Record actual evidence against the tested revision and platform.
- At each completed or interrupted material increment, add or update one pilot entry. Link existing evidence, identify any next action, and record observed skill use and exceptions. For routine edits, the diff and a focused check suffice unless the edit reveals a recurring workflow issue.
- Keep repository records suitable for sharing. Use synthetic examples or redact sensitive inputs and logs. Keep consequential product decisions and external actions within the authority given for the task.
