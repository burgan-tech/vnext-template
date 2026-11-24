# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- `validate.js` **authoring lint** (warnings only; the run still passes). It catches patterns that pass the JSON
  schemas but break on the cluster or in the client. Measured 24.11.2026 on ocptest with the staff back-office client:
  - a `showIf` / `hideIf` on a view **component**: the renderer ignores it and draws every guarded block.
    Conditionals belong in schema `x-conditional`; a component uses `visible`;
  - a `urn:amorphie:wf:…` button `command`: no client parses it, so the click ends in "No handler";
  - `urn:vnext:flow:transition:<d>:<f>:${param}:<tx>` on a view: the back-office client does not fill `${param}`
    there and asks for instance `${param}` (404);
  - a master schema that requires fields the start schema does not: the start passes and then fails on the
    master (500 "Start transition validation failed");
  - a flow offered with a back-office **New** button (a `records/*.json` menu item with `payload.create`) whose
    start schema has `required`: New sends an empty body, so every click fails and no record is created.
- AI Agent support (burgan-tech/vnext#1105, experimental):
  - `Agents/` component folder (`sys-agents`) and `paths.agents` / `exports.agents` in `vnext.config.json`
  - `getAgents()` in `index.js`; `paths.agents` defaults to `Agents` in `index.js`, `setup.js` and `build.js`
  - `validate.js` validates `Agents/**` against the `agent` schema of `@burgan-tech/vnext-schema` when the
    installed package provides it, and warns when it does not
  - Examples: `triage-agent` (provider `local`) with a dry-run `.http` request, the `triage-flow` AiAgent
    workflow (`stateType: 6`) with an `.http` walkthrough, and an MCP task (type 24) under `examples/mcp/`
    whose credentials are `secretRef`s only
- `.http` files are text: `init.js` and `setup.js` replace the domain-name placeholder in them
- `init.js` - Script for creating new projects via `npx @burgan-tech/vnext-template <domain-name>`
- `setup.js` - Interactive setup script for replacing `{domainName}` in template files
- Support for domain name via command-line argument in `npx` command
- Support for domain name via `DOMAIN_NAME` environment variable
- Binary commands: `@burgan-tech/vnext-template`, `vnext-template`, and `vnext-setup`
- Automatic domain name replacement during installation
- Validation instructions in README

### Changed
- Simplified README to focus on installation and usage
- Updated `package.json` to include bin entries for npx commands
- Updated `package.json` files array to exclude development-only files (test.js, test-domain-detection.sh, CHANGELOG.md, .cursorrules, .gitignore, .gitattributes)
- `postinstall` script now runs setup.js automatically
- Improved package structure for npm publishing

### Deprecated

### Removed
- Development-only files from published package (test.js, test-domain-detection.sh, .cursorrules, .gitignore, .gitattributes)

### Fixed
- `sync-schema-version.js` now also updates `@burgan-tech/vnext-schema` in `devDependencies` when it is listed
  there. Previously only `dependencies` moved. Because `^0.0.x` pins exactly one version, the devDependency kept
  `^0.0.38` and `validate.js` could resolve the old schema package.

### Security

## [1.0.0] - 2024-12-19

### Added
- Initial release of @burgan-tech/vnext-template
- Domain-based project structure with template folders
- Core module with component access functions:
  - `getDomainConfig()` - Access domain configuration
  - `getSchemas()` - Load schema definitions
  - `getWorkflows()` - Load workflow definitions
  - `getTasks()` - Load task definitions
  - `getViews()` - Load view definitions
  - `getFunctions()` - Load function definitions
  - `getExtensions()` - Load extension definitions
  - `getAvailableTypes()` - Get available component types
  - `getDomainName()` - Get domain directory name
- JSON validation for all component files
- Comprehensive test suite with 10 test cases
- Package validation with 8 validation checks
- NPM package configuration with @burgan-tech scope
- GitHub Actions workflow for automated publishing
- Support for both NPM.js and GitHub Packages registries
- Semantic versioning with automated version calculation
- Complete documentation and README
- MIT license

### Features
- **Template Structure**: Ready-to-use project structure for vNext components
- **Component Loading**: Dynamic loading of JSON-based component definitions
- **Validation**: Built-in validation for JSON schemas and package structure
- **Publishing**: Automated CI/CD pipeline for package publishing
- **Scoped Package**: Published under @burgan-tech organization scope
- **Multi-Registry**: Support for publishing to NPM and GitHub Packages
- **Documentation**: Comprehensive README with usage examples and API documentation

### Technical Details
- Node.js 16+ compatibility
- JSON Schema validation support
- Domain-driven architecture pattern
- Modular component organization
- Cross-platform compatibility (Windows, macOS, Linux)
- GitHub Actions integration
- Automated testing and validation

### Package Contents
- Core module (`index.js`)
- Test suite (`test.js`)
- Validation script (`validate.js`)
- Domain template structure (`{domainName}/`)
- Configuration files (`package.json`, `vnext.config.json`)
- Documentation (`README.md`, `CHANGELOG.md`)
- CI/CD workflow (`.github/workflows/build-and-publish.yml`)

[Unreleased]: https://github.com/burgan-tech/vnext-template/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/burgan-tech/vnext-template/releases/tag/v1.0.0
