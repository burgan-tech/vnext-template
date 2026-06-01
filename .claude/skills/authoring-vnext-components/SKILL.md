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

Components normally **omit** a `$schema` field (validation maps folder → schema
automatically); only add one if you mirror an existing component that has it.

```jsonc
{
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

### Conventions

- **kebab-case** for all `key`s and file names; **PascalCase** for C# `.csx` class names.
- JSON: **2-space indentation**, double quotes, no trailing commas.
- `version`/`flowVersion` are semver; bump appropriately for breaking vs. non-breaking changes.
- A `.meta` folder may sit next to components — it is ignored by validation; don't put components there.

### Cross-component references

Reference another component with the nested shape (target component's own `flow`):

```json
{ "key": "create-bank-account", "domain": "core", "flow": "sys-tasks", "version": "1.0.0" }
```

With `strictMode` on, every reference must resolve to an existing key+version of the
right type. Workflow `startTransition.target` and each transition `target` must name a
defined state.

## Type-specific `attributes`

Confirm the exact shape against the schema each time; this is the gist:

- **workflow** — required `type`, `states`, `startTransition`, `labels`. `type` is a
  letter (`"S"`, `"F"`, `"P"`, `"C"`, …). Also supports `timeout`, `functions`,
  `features`, `sharedTransitions`, `extensions`, `errorBoundary`, `cancel`, `exit`,
  `updateData`, `schema`, `queryRoles`. Transitions have a `triggerType`
  (`0` manual, `1` auto/rule, `2` timer, `3` event); auto transitions must come in
  complementary pairs with mutually exclusive rules (or a single always-true rule).
  **Domain rule — every workflow MUST declare a master payload schema** at
  `attributes.schema.schema` (a nested reference `{ key, domain, flow: "sys-schemas",
  version }`), even though the JSON schema marks it optional. `npm run validate`
  enforces this. Author the referenced schema component first, and normally point the
  `startTransition.schema` at the same schema so the start payload is validated.
- **task** — required `type` (enum `"1"`–`"15"`, e.g. `"6"`=HTTP, `"7"`=Script,
  `"15"`=GetInstances — verify against docs) and `config`.
- **view** — required `type` (integer) and `content`; optional `labels`, `display`.
- **function** — required `scope` (enum `D`/`F`/`I`) and `task` (object with
  `order`, `task`, `mapping`); optional `labels`, `roles`.
- **extension** — required `type` (integer), `scope` (integer), `task`; optional `labels`.
- **schema** — required `type` (enum `workflow`/`task`/`function`/`view`/`schema`/
  `extension`/`headers`) and `schema`; optional `labels`.

## C# mapping & rule files (`.csx`)

Workflows transform data and evaluate conditions with C# scripts kept in a `src/`
folder next to the workflow JSON. They are referenced from transitions, tasks, and
`onExecutionTasks` via a `mapping` object:

```json
"mapping": { "location": "./src/CreateBankAccountMapping.csx", "code": "<base64>", "encoding": "base64" }
```

- **Never hand-edit `code` / manually base64-encode.** The vNext VS Code extension
  auto-encodes the `.csx` into `code` on save. Author the `.csx`; leave `code` to the tool.
- Mappings implement `IMapping` (`InputHandler`/`OutputHandler` returning `ScriptResponse`);
  rules implement the condition interface and return a boolean for auto-transition `rule`s.
- File names kebab/Pascal per existing convention; **class names are PascalCase**.

## Workflow

1. Identify the component type and read the matching schema file (and any examples
   already present in the domain folder — mirror existing conventions).
2. Write the JSON into the correct subfolder, filename == `key`. For a workflow,
   author any referenced `.csx` mappings/rules under its `src/` folder, and provide a
   `.http` test file alongside it that exercises start → transitions → state queries.
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
