---
description: Scaffold a new vNext domain component (workflow/task/view/function/extension/schema)
argument-hint: "<type> <key> [short description]"
---

Scaffold a new vNext component. Use the **authoring-vnext-components** skill for the
component structure, field rules, and validation workflow.

Parse `$ARGUMENTS` as: `<type> <key> [description]`, where `<type>` is one of
`schema | workflow | task | view | function | extension`. If the type or key is
missing or `<type>` is not one of those, ask me before generating anything.

Steps:
1. Read the matching schema in `node_modules/@burgan-tech/vnext-schema/schemas/`
   (run `npm install` first if it's missing) and any existing component in the same
   folder to mirror conventions.
2. Read the project `domain` from [vnext.config.json](vnext.config.json).
3. Create the JSON file in the correct domain subfolder, filename == `<key>.json`,
   with the full common envelope (correct `flow` for the type, semver `version`,
   matching `domain`) and a minimal-but-valid `attributes` for that type.
4. Run `npm run validate` and fix any errors until it passes.
5. Tell me whether the component should be added to `exports` in vnext.config.json
   for cross-domain use, and do it if I confirm.
