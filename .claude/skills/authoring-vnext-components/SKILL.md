---
name: authoring-vnext-components
description: >-
  Create, edit, and validate vNext workflow domain components (Schemas, Workflows,
  Tasks, Views, Functions, Extensions) in a @burgan-tech/vnext-template project.
  Use whenever the user wants to add or change a component JSON file under the
  domain directory, asks about the required component fields (key/version/domain/
  flow/flowVersion/tags/attributes), or hits schema-validation errors from
  `npm run validate`.
---

# Authoring vNext components

This repo is a **@burgan-tech/vnext-template** project: a single *domain* made of
JSON component files that are validated against JSON Schemas shipped in
`@burgan-tech/vnext-schema`. Your job in this skill is to add or modify those
component files so they pass `npm run validate` and build cleanly.

## Project layout

Components live under the domain directory (the placeholder `{domainName}/` in the
template, or the real domain name once `npm run setup <name>` has run). Subfolders
map 1:1 to component types via `paths` in [vnext.config.json](vnext.config.json):

| Folder        | Component type | `flow` value     | Schema file in vnext-schema        |
|---------------|----------------|------------------|------------------------------------|
| `Schemas/`    | schema         | `sys-schemas`    | `schema-definition.schema.json`    |
| `Workflows/`  | workflow       | `sys-flows`      | `workflow-definition.schema.json`  |
| `Tasks/`      | task           | `sys-tasks`      | `task-definition.schema.json`      |
| `Views/`      | view           | `sys-views`      | `view-definition.schema.json`      |
| `Functions/`  | function       | `sys-functions`  | `function-definition.schema.json`  |
| `Extensions/` | extension      | `sys-extensions` | `extension-definition.schema.json` |

**Always read the authoritative schema before writing or editing a component.**
They are the source of truth and may change with the schema version pinned in
[package.json](package.json) (`@burgan-tech/vnext-schema`). Find them at:

```
node_modules/@burgan-tech/vnext-schema/schemas/<schema-file>.json
```

If `node_modules` is absent, run `npm install` first, or read the schema via
`npm pack @burgan-tech/vnext-schema && tar -xzf *.tgz`.

## Common envelope (every component)

All component types share the core envelope from `core-schema.schema.json`.
Required top-level fields: `key`, `version`, `domain`, `flow`, `flowVersion`,
`tags`, `attributes`.

```jsonc
{
  "$schema": "../../node_modules/@burgan-tech/vnext-schema/schemas/<schema-file>.json",
  "key": "my-component",          // pattern ^[a-z0-9-]+$
  "version": "1.0.0",             // ^\d+\.\d+\.\d+(-[a-zA-Z]+\.\d+)?$
  "domain": "<the-project-domain>",// pattern ^[a-z0-9-]+$ (matches vnext.config.json domain)
  "flow": "sys-flows",            // fixed per component type — see table above
  "flowVersion": "1.0.0",
  "tags": ["..."],
  "attributes": { /* type-specific — see below */ }
}
```

- `domain` must match the `domain` in [vnext.config.json](vnext.config.json).
- Keep the JSON filename consistent with `key` — `referenceResolution.schemaValidationRules.enforceFilenameConsistency` is on.
- `allowUnknownProperties` is **false**: do not add fields the schema doesn't define.

## Type-specific `attributes`

Confirm the exact shape against the schema each time; this is the gist:

- **workflow** — required `type`, `states`, `startTransition`, `labels`. Also
  supports `timeout`, `functions`, `features`, `sharedTransitions`, `extensions`,
  `errorBoundary`, `cancel`, `exit`, `updateData`, `schema`, `queryRoles`.
- **task** — required `type` (enum `"1"`–`"15"`) and `config`.
- **view** — required `type` (integer) and `content`; optional `labels`, `display`.
- **function** — required `scope` (enum `D`/`F`/`I`) and `task` (object with
  `order`, `task`, `mapping`); optional `labels`, `roles`.
- **extension** — required `type` (integer), `scope` (integer), `task`; optional `labels`.
- **schema** — required `type` (enum `workflow`/`task`/`function`/`view`/`schema`/
  `extension`/`headers`) and `schema`; optional `labels`.

## Workflow

1. Identify the component type and read the matching schema file (and any examples
   already present in the domain folder — mirror existing conventions).
2. Write the JSON into the correct subfolder, filename == `key`.
3. Register exports if the component is meant to be shared cross-domain: add the
   filename to the right array under `exports` in [vnext.config.json](vnext.config.json).
4. Validate: `npm run validate`. It prints clickable `file://path:line` links for
   each failure and a failed-files summary.
5. Fix reported errors against the schema and re-run until clean.
6. If the change is for cross-domain consumption, sanity-check `npm run build:reference`.

## Validation notes

- `npm run validate` checks package structure, `vnext.config.json`, domain dirs,
  JSON syntax, and every component against its schema.
- Errors include a JSON pointer (e.g. `/attributes/states/0/transitions/1`) — use
  it to locate the offending node.
- Common failures: `key`/`domain`/`flow` not matching the `^[a-z0-9-]+$` pattern,
  non-semver `version`, unknown extra properties, missing required `attributes`.
