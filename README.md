---
name: Skills Library
description: Reference index for this repository: what each skill enforces, how to install and invoke them, and the supported frontmatter fields. Informational only, not an executable workflow.
slash: false
metadata:
  opencode/autoinvoke: false
---

# skills

Personal, opinionated skill library for [OpenCode](https://opencode.ai).

This repository doubles as the backup and distribution point for my
`~/.config/opencode/skills` directory. Every file here is a skill: reusable,
opinionated instructions that steer an agent through a specific task the way I
want it done.

Skills are plain Markdown with optional YAML frontmatter. The skill ID comes
from the file path, not from frontmatter, so `tdd.md` is loaded with
`skill({ id: "tdd" })`.

## Catalog

| Skill                   | ID       | File                 | What it enforces                                                                |
| ----------------------- | -------- | -------------------- | ------------------------------------------------------------------------------- |
| test driven development | `tdd`    | [`tdd.md`](./tdd.md) | Strict Red-Green-Refactor for TypeScript, driven only by pre-agreed test seams  |
| Skills Library          | `README` | `README.md`          | Informational index of this repo. Not autoinvoked                              |

## tdd — test driven development

**Triggers:** planning or implementing TypeScript features via TDD, defining
testing boundaries, or setting up automated quality pipelines with Bun.

**Excludes:** end-to-end (E2E) testing, legacy JavaScript, and UI styling
without logic.

The core opinion: **tests are written only against agreed seams.** Before any
code is written, the entry seam (HTTP controller, public module) and the exit
seam (database client, external API) are mapped and explicitly agreed with the
user. Everything inside the slice is then a black box.

### Workflow

1. **Discover context** — check the workspace for `biome.json` and `.husky/`.
2. **Define the seam** — map entry and exit seams, then stop for user agreement.
3. **Write the failing test** — against the entry seam only, mocking at the exit
   seam, using `bun test`.
4. **Implement the vertical slice** — the minimum TypeScript and Zod needed to
   satisfy the test.
5. **Pass the test** — `bun test` until the seam contract is fulfilled.
6. **Refactor internals** — `bunx @biomejs/biome check --write .`, test stays
   green and untouched.

### Non-negotiables

- **Integration tests are primary:** entry seam is a router or controller, exit
  seam is a third-party API. Unit tests are secondary: entry seam is a public
  module, exit seam is a database client or filesystem.
- **No test leaks:** tests never touch private methods or internal helpers, so
  internals stay free to change.
- **No ghost code:** no branch, parameter, or edge-case handler enters
  production unless a failing seam test drives it.
- **Tooling:** Bun for scripts and tests, Biome for format and lint, Zod for
  validation at architectural boundaries.

Full instructions: [`tdd.md`](./tdd.md).

## Usage

### Install every skill

```sh
git clone https://github.com/gilbertoesp/skills.git ~/.config/opencode/skills
```

### Install a single skill

```sh
curl -O https://raw.githubusercontent.com/gilbertoesp/skills/main/tdd.md
mv tdd.md ~/.config/opencode/skills/
```

### Register a local clone as an extra source

```jsonc title="~/.config/opencode/opencode.jsonc"
{
  "$schema": "https://opencode.ai/config.json",
  "skills": ["~/dev/skills"],
}
```

Relative paths resolve from the OpenCode working directory, `~/` from the home
directory, and absolute paths are used as written. Entries are merged, not
replaced.

### Invoke

At each model step OpenCode advertises the ID, name, and description of every
permitted skill, then loads the full body on request. Just ask for a skill by
name or ID:

```
Use the tdd skill to plan the checkout feature
```

## Conventions

- One skill per file, named in lowercase kebab-case so the file name and the
  skill ID match.
- Every file at the source root is a skill, so every file needs frontmatter.
  Behavioral skills stay advertised; documentation opts out with
  `metadata.opencode/autoinvoke: false`.
- The `description` states the trigger and the exclusions, not the
  implementation.
- The skill file is the single source of truth. This README summarizes and
  links; it does not duplicate the instructions.
- Flat `*.md` files belong at the source root, such as `skills/tdd.md`.
  Supporting files must live inside a directory whose entry point is named
  exactly `SKILL.md`, such as `skills/git-release/SKILL.md`.

### Frontmatter

All frontmatter is optional at runtime, but a skill without a `description` is
never advertised to the model.

| Field                          | Behavior                                                          |
| ------------------------------ | ----------------------------------------------------------------- |
| `name`                         | Display label. The ID still comes from the file path              |
| `description`                  | Summary used to decide when to offer the skill to the model       |
| `slash: false`                 | Hide the skill from the interactive command list                   |
| `metadata.opencode/slash`      | Boolean or `"true"` / `"false"`; overrides `slash`                 |
| `metadata.opencode/autoinvoke` | `false` omits the skill from the model's available list            |

OpenCode also accepts the portability fields `license` and `compatibility`, but
does not interpret them.

### Bounding a skill with frontmatter

Frontmatter is what keeps a skill from bleeding into unrelated tasks. Every
behavioral skill should declare a `description` that names both its trigger and
its exclusions, so the model knows when *not* to reach for it.

Informational files such as this README are bounded the other way: they are
still valid skills, but they are withdrawn from the model's attention and from
the command catalog.

```yaml
---
name: Skills Library
description: Informational index, not an executable workflow.
slash: false
metadata:
  opencode/autoinvoke: false
---
```

`autoinvoke: false` removes the skill from the list offered to the model at
each step. The skill stays registered and can still be loaded explicitly by ID,
so nothing is lost by opting out.

See the full reference in the
[skills documentation](https://opencode.ai/v2/docs/skills/).
