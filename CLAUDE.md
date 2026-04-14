# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is a Claude Code plugin marketplace hosted at `indradhanush/skills`. It distributes personal Claude Code skills via the `/plugin` system.

## Adding a new skill

1. Create the plugin directory structure:
   ```
   plugins/<skill-name>/.claude-plugin/plugin.json
   plugins/<skill-name>/skills/<skill-name>/SKILL.md
   ```

2. `plugin.json` — minimal required fields:
   ```json
   {
     "name": "<skill-name>",
     "description": "<one-line description>",
     "version": "1.0.0"
   }
   ```

3. `SKILL.md` — required YAML frontmatter:
   ```markdown
   ---
   name: <skill-name>
   description: >
     <trigger description — this is what the model reads to decide when to activate the skill>
   ---

   <skill instructions>
   ```

4. Register in `.claude-plugin/marketplace.json` under `plugins`:
   ```json
   {
     "name": "<skill-name>",
     "source": "./plugins/<skill-name>",
     "description": "<one-line description>"
   }
   ```

5. Add an entry to `README.md` under `## Skills` with the skill name, a one-line description, and the install command.

## Modifying an existing skill

- Update `SKILL.md` and/or `plugin.json` as needed.
- Bump `version` in `plugin.json`.
- Update the skill's entry in `README.md` if the description or behavior changed.

## Removing a skill

- Delete `plugins/<skill-name>/`.
- Remove its entry from `.claude-plugin/marketplace.json`.
- Remove its entry from `README.md`.

## Validate before pushing

```bash
claude plugin validate .
```

Or from within Claude Code:
```
/plugin validate .
```

## Install from this marketplace

```
/plugin marketplace add indradhanush/skills
/plugin install <skill-name>@indradhanush-skills
```

## Key files

- `.claude-plugin/marketplace.json` — marketplace catalog; `name` field here is the `@marketplace` suffix users type when installing
- `plugins/<name>/.claude-plugin/plugin.json` — per-plugin manifest; `version` controls update detection
- `plugins/<name>/skills/<name>/SKILL.md` — skill instructions and trigger description

## Versioning

Bump `version` in `plugin.json` for every published change. Claude Code uses the version to detect updates — unchanged versions are skipped.
