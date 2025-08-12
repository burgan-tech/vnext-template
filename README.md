# {packageName}

A domain-driven vNext project created using [@vnext/cli](https://www.npmjs.com/package/@vnext/cli).

## 📋 Table of Contents

- [Getting Started](#-getting-started)
- [Project Structure](#-project-structure)
- [Domain Configuration](#-domain-configuration)
- [Component Types](#-component-types)
- [File Naming Conventions](#-file-naming-conventions)
- [VSCode Integration](#-vscode-integration)
- [Available Scripts](#-available-scripts)
- [Development Workflow](#-development-workflow)
- [Validation & Linting](#-validation--linting)
- [Best Practices](#-best-practices)

## 🚀 Getting Started

### Prerequisites
- Node.js 16+ 
- npm or yarn
- VSCode (recommended for full IDE experience)

### Installation

```bash
# Install dependencies
npm install
```

## 📁 Project Structure

```
{packageName}/
├── {domainName}/                    # Domain-specific components
│   ├── Workflows/                   # Business process workflows
│   │   ├── workflow-1.json
│   │   └── workflow-2.json
│   ├── Functions/                   # Serverless functions
│   │   ├── function-1.json  
│   │   └── function-2.json
│   ├── Views/                       # UI views and components
│   │   ├── view-1.json
│   │   └── view-2.json
│   ├── Extensions/                  # Custom extensions
│   │   ├── extension-1.json
│   │   └── extension-2.json
│   ├── Schemas/                     # Data models and schemas
│   │   ├── schema-1.json
│   │   └── schema-2.json
│   └── Tasks/                       # Background tasks
│       ├── task-1.json
│       └── task-2.json
│   ├── vnext.code-snippets       # Code snippets for components
├── vnext.config.json             # Project configuration
├── .cursorrules                     # Cursor AI assistant rules
├── package.json                     # Dependencies and scripts
└── README.md                        # This documentation
```

## ⚙️ Domain Configuration

### vnext.config.json

The `vnext.config.json` file contains domain-specific configuration:

```json
{
  "domain": "{domainName}",
  "version": "1.0.0",
  "runtimeVersion": "1.0.0",
  "paths": {
    "workflows": "{domainName}/Workflows",
    "functions": "{domainName}/Functions", 
    "views": "{domainName}/Views",
    "extensions": "{domainName}/Extensions",
    "schemas": "{domainName}/Schemas",
    "tasks": "{domainName}/Tasks"
  }
}
```

#### Configuration Properties

- **domain**: The business domain name for this project
- **validation.enabled**: Enable/disable component validation
- **validation.strict**: Use strict validation rules
- **validation.rules**: Individual validation rule toggles
- **paths**: Directory paths for each component type
- **linting**: File inclusion/exclusion patterns and rules

## 🧩 Component Types

### 1. Workflows
Business process definitions with states, transitions, and tasks.

**Purpose**: Define complex business workflows with multiple states and transitions.

**Schema**: `workflow-definition.schema.json`

### 2. Functions  
Serverless function definitions for business logic.

**Purpose**: Define stateless functions for data processing and business rules.

**Schema**: `function-definition.schema.json`

### 3. Views
UI component and view definitions.

**Purpose**: Define user interface components and their data bindings.

**Schema**: `view-definition.schema.json`

### 4. Extensions
Custom extensions for extending framework capabilities.

**Purpose**: Add custom functionality to the vNext framework.

**Schema**: `extension-definition.schema.json`

### 5. Schemas
Data model and schema definitions.

**Purpose**: Define data structures, validation rules, and API contracts.

**Schema**: `schema-definition.schema.json`

### 6. Tasks
Background task and job definitions.

**Purpose**: Define scheduled jobs, queued tasks, and background processes.

**Schema**: `task-definition.schema.json`

## 📏 Best Practices

### Component Design
1. **Single Responsibility**: Each component should have one clear purpose
2. **Consistent Naming**: Follow domain naming conventions strictly
3. **Version Management**: Use semantic versioning for all components
4. **Documentation**: Include clear descriptions and examples
5. **Validation**: Always validate components before committing

### File Organization
1. **Domain Grouping**: Keep all domain components together
2. **Type Separation**: Separate components by type in dedicated folders
3. **Descriptive Names**: Use meaningful, searchable filenames
4. **Consistent Structure**: Follow the established JSON structure

### Development Process
1. **Use Snippets**: Leverage VSCode snippets for consistency
2. **Validate Early**: Run validation during development
3. **Lint Regularly**: Use linting to catch issues early
4. **Test Thoroughly**: Validate components in different scenarios
5. **Document Changes**: Update documentation when adding features

### Performance
1. **Optimize JSON**: Keep JSON files lean and focused
2. **Batch Operations**: Use batch scripts for multiple component updates
3. **Watch Mode**: Use file watching for automatic updates
4. **Caching**: Leverage validation caching for faster development

## 📚 Learn More

- [vNext Documentation](https://github.com/burgan-tech/vnext-cli)
- [vNext CLI](https://www.npmjs.com/package/@vnext/cli)

---

**Domain**: {domainName} | **Generated by**: [@vnext/cli](https://www.npmjs.com/package/@vnext/cli)