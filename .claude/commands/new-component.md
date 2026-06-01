---
description: Scaffold a new vNext domain component (workflow/task/view/function/extension/schema)
argument-hint: "<type> <key> [short description]"
---

Scaffold a new vNext component. Use the **authoring-vnext-components** skill for the
component structure, field rules, and validation workflow.

Parse `$ARGUMENTS` as: `<type> <key> [description]`, where `<type>` is one of
`schema | workflow | task | view | function | extension`.

**Gathering missing inputs — ask before generating anything, prefer popups:**
- If `<type>` is missing or not one of the six, ask via `AskUserQuestion` with the six
  types as options.
- If `<key>` is missing, ask for it as **free text in plain prose** and wait — do NOT
  invent suggested flow/component names. (`AskUserQuestion` can't help here: it requires
  ≥2 predefined options and a free-text-only question fails with `too_small`; the user
  does not want fabricated name suggestions just to satisfy that minimum.) Ask the
  `<key>` (kebab-case, becomes the filename) and a one-line description together in prose.

**Precondition — the project domain must be initialized.** Before doing anything else,
read `domain` from [vnext.config.json](vnext.config.json). If it is still the template
placeholder `{{DOMAIN_NAME}}` (or the `{{DOMAIN_NAME}}/` folder still exists), the project is
not yet initialized:
- Ask me (plain text) for the domain name (kebab-case, e.g. `payments`, `lending`).
- Initialize it. `setup.js`/`npm run setup <name>` short-circuits once the template
  folders exist, so do it directly: replace `{{DOMAIN_NAME}}` → `<name>` in
  `vnext.config.json` (`domain`, `componentsRoot`, description, exports keywords) and in
  `package.json` (the `files` entry), then rename the `{{DOMAIN_NAME}}/` folder and
  `{{DOMAIN_NAME}}.link.json` to `<name>`. Confirm `npm run validate` is green before
  scaffolding.

**Do not scaffold a generic minimal skeleton.** A component is only useful once its
content is defined, so clarify *what it should contain* before writing any JSON.

Steps:
1. **Clarify the content with the `analyst` agent first.** Hand the analyst the
   `<type>`, `<key>`, and any `[description]`. The analyst inspects the domain and
   [vnext.config.json](vnext.config.json) and turns the request into the concrete
   content the component needs for its type — e.g. for a **workflow**: states,
   start/transitions, and which tasks/schemas it references; for a **task**: the task
   `type` and `config` (endpoint/binding/script) and its mapping; for a **schema**:
   the payload fields and constraints; for **function/view/extension**: the relevant
   attributes. The analyst flags ambiguities as questions.
2. **Ask me the analyst's open questions** and wait for the answers. Use
   `AskUserQuestion` only for questions with a small set of fixed choices (give a
   recommended default first); ask anything free-form (names, field lists, free text)
   in plain prose. Don't guess the component's behavior — that's the whole point.
3. Read the matching schema in `node_modules/@burgan-tech/vnext-schema/schemas/`
   (run `npm install` first if it's missing) and any existing component in the same
   folder to mirror conventions. For non-trivial components, run the `architect` →
   `component-author` agents on the analyst's plan; for simple ones, author directly.
4. Read the project `domain` from [vnext.config.json](vnext.config.json).
5. Create the JSON file in the correct domain subfolder, filename == `<key>.json`,
   with the full common envelope (correct `flow` for the type, semver `version`,
   matching `domain`) and an `attributes` that reflects the **clarified content** from
   steps 1–2 (not an empty placeholder). Author any referenced tasks/schemas and
   `.csx` sources the design calls for.
6. Run `npm run validate` and fix any errors until it passes.
7. Tell me whether the component should be added to `exports` in vnext.config.json
   for cross-domain use, and do it if I confirm.
8. **Document it with the `doc-writer` agent.** Once the component validates,
   doc-writer writes `docs/<Type>/<key>.md` (one file per component, mirroring the
   domain folders) — **creating** it if absent, **updating** it in place if it
   already exists — and adds a [CHANGELOG.md](CHANGELOG.md) entry. Any referenced
   tasks/schemas authored alongside the component get their own docs too.
