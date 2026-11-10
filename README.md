# {domainName}

A structured template package for vNext workflow components with domain-based architecture.

## 📦 Installation

Create a new project with your domain name:

```bash
npx @burgan-tech/vnext-template <domain-name>
```

### Example

```bash
npx @burgan-tech/vnext-template user-management
```

This will create a new directory with your domain name, copy all template files, replace `{domainName}` instances, and install dependencies automatically.

### Install specific version

```bash
npx @burgan-tech/vnext-template@<version> <domain-name>
```

Example:

```bash
npx @burgan-tech/vnext-template@1.0.0 user-management
```

### Alternative: Install as dependency

```bash
npm install @burgan-tech/vnext-template
npm run setup <domain-name>
```

Or install specific version:

```bash
npm install @burgan-tech/vnext-template@<version>
npm run setup <domain-name>
```

Or use environment variable:

```bash
DOMAIN_NAME=user-management npm install @burgan-tech/vnext-template@<version>
```

## 🚀 Usage

After installation, your project structure will be:

```
<domain-name>/
├── Agents/          # AI agents (sys-agents), with the triage-agent example
├── Extensions/
├── Functions/
├── Schemas/
├── Tasks/
├── Views/
├── Mappings/
└── Workflows/       # with the triage-flow AiAgent example
examples/
└── mcp/             # MCP task example (type 24), not published until you copy it
```

### Access components in code

```javascript
const vnextTemplate = require('@burgan-tech/vnext-template');

// Get domain configuration
const config = vnextTemplate.getDomainConfig();

// Get paths configuration
const paths = vnextTemplate.getPathsConfig();

// Get all schemas
const schemas = vnextTemplate.getSchemas();

// Get all workflows
const workflows = vnextTemplate.getWorkflows();

// Get all tasks
const tasks = vnextTemplate.getTasks();

// Get component path for a specific type
const schemasPath = vnextTemplate.getComponentPath('schemas');
```

### Available methods

| Method | Description |
|--------|-------------|
| `getDomainConfig()` | Get domain configuration from vnext.config.json |
| `getPathsConfig()` | Get paths configuration with defaults |
| `getSchemas()` | Get all schemas |
| `getWorkflows()` | Get all workflows |
| `getTasks()` | Get all tasks |
| `getViews()` | Get all views |
| `getFunctions()` | Get all functions |
| `getExtensions()` | Get all extensions |
| `getMappings()` | Get all mappings |
| `getAgents()` | Get all AI agents (sys-agents) |
| `getDomainName()` | Get domain directory name |
| `getAvailableTypes()` | Get list of available component types |
| `getComponentPath(type)` | Get full path for a component type |

## ⚙️ Configuration

The `vnext.config.json` file allows you to customize paths and exports:

```json
{
  "domain": "my-domain",
  "paths": {
    "componentsRoot": "my-domain",
    "schemas": "Schemas",
    "workflows": "Workflows",
    "tasks": "Tasks",
    "views": "Views",
    "functions": "Functions",
    "extensions": "Extensions",
    "mappings": "Mappings",
    "agents": "Agents"
  },
  "exports": {
    "schemas": ["schema1.json", "schema2.json"],
    "workflows": ["workflow1.json"],
    "tasks": [],
    "views": [],
    "functions": [],
    "extensions": [],
    "mappings": [],
    "agents": []
  }
}
```

### Path Configuration

You can customize component directory names:

```json
{
  "paths": {
    "componentsRoot": "src",
    "workflows": "Flows",
    "schemas": "Models"
  }
}
```

`paths.agents` is optional: `index.js`, `validate.js` and `build.js` default it to `Agents`. The vNext
CLI (`wf sync`) only publishes the agents folder when `paths.agents` is declared, and publishes it
before the workflows that reference the agents.

## 🤖 AI agents (experimental)

An `AiAgent` state (`stateType: 6`) runs an AI agent when an instance enters it; the agent's decision
picks one of the state's automatic transitions without a rule. The agent itself is a `sys-agents`
component in `Agents/`. A new project ships a working example:

| File | What it shows |
|------|---------------|
| `Agents/triage-agent.json` | Agent on the `local` provider (deterministic, no network): instructions, a script tool, output schema, policy |
| `Agents/triage-agent.http` | `POST /api/v1/{domain}/definitions/agents/{key}/dry-run` with `prompt`, `data` and optional `candidates` |
| `Workflows/triage-flow.json` | AiAgent state with a model decision, a `fallback`, `onExhausted` and transition `description`s |
| `Workflows/triage-flow.http` | Start an instance, read its state, data and `functions/agent-runs` |
| `examples/mcp/` | MCP task (type 24) with credentials only as `{ "secretRef": "…" }`, also usable as an agent tool |

Things to know:

- `provider` names a provider profile configured on the runtime (`AiAgent:Providers`), never an endpoint
  or a key. The `local` profile exists by default.
- The dry-run endpoint answers only callers whose `role` header is listed in the runtime's
  `AiAgent:DryRun:Roles` (empty disables it).
- An MCP task's endpoint host must be in the runtime's `Mcp:AllowedHosts` at publish time, which is why
  the example stays outside the domain folder until you need it.
- Never put a secret in component JSON; credentials are always `secretRef`s.
- Validating `Agents/` and `stateType: 6` needs a `@burgan-tech/vnext-schema` with the AI Agent state
  (`schemaVersion` 0.0.60 or later). With an older schema package `npm run validate` warns that
  `Agents/` is not schema-validated and rejects the AiAgent flow; delete the examples if you stay on it.

## ✅ Validation

Validate your project structure and schemas:

```bash
npm run validate
```

This will check:
- Package.json structure and content
- Main entry point functionality
- vnext.config.json validation
- Domain directory structure
- JSON file syntax validation
- Schema validation using @burgan-tech/vnext-schema
- Module functionality
- Semantic versioning compliance

### Validation Output

The validation provides detailed output with:
- ✅ Passed validations
- ❌ Failed validations with file paths and line numbers
- 📊 Summary statistics
- 📋 Failed files summary for easy navigation

## 🏗️ Build

Build your domain package for deployment or cross-domain usage:

```bash
# Runtime build (default) - Complete domain structure
npm run build

# Reference build - Only exported components
npm run build:reference

# Runtime build explicitly
npm run build:runtime
```

### Build Options

```bash
npm run build -- [options]

Options:
  -o, --output <dir>     Output directory (default: dist)
  -t, --type <type>      Build type: reference or runtime (default: runtime)
  --skip-validation      Skip validation during build
  -h, --help             Show help message
```

### Build Types

| Type | Description | Use Case |
|------|-------------|----------|
| `runtime` | Complete domain structure with all files | Engine deployment |
| `reference` | Only exported components from vnext.config.json | Cross-domain usage |

### Examples

```bash
# Build to custom directory
npm run build -- -o my-build

# Reference build to custom directory
npm run build -- -t reference -o packages/ref

# Skip validation for faster builds
npm run build -- --skip-validation
```

### Build Output Structure

**Runtime Build:**
```
dist/
├── <domain>/
│   ├── Agents/
│   ├── Extensions/
│   ├── Functions/
│   ├── Schemas/
│   ├── Tasks/
│   ├── Views/
│   ├── Mappings/
│   └── Workflows/
├── vnext.config.json
├── package.json
├── README.md
└── LICENSE
```

**Reference Build:**
```
dist/
├── <domain>/
│   ├── Agents/         # Only exported files
│   ├── Extensions/     # Only exported files
│   ├── Functions/      # Only exported files
│   ├── Schemas/        # Only exported files
│   ├── Tasks/          # Only exported files
│   ├── Views/          # Only exported files
│   ├── Mappings/       # Only exported files
│   └── Workflows/      # Only exported files
├── vnext.config.json
├── package.json
├── README.md
└── LICENSE
```

## 📜 Available Scripts

| Script | Description |
|--------|-------------|
| `npm run validate` | Validate project structure and schemas |
| `npm run build` | Build runtime package to dist/ |
| `npm run build:runtime` | Build runtime package explicitly |
| `npm run build:reference` | Build reference package with exports only |
| `npm run setup <name>` | Setup domain with given name |
| `npm run sync-schema` | Sync schema version from dependencies |
| `npm test` | Run tests |

## 📄 License

MIT
