# kitn Registry

Public directory of kitn component registries.

Browse components at **[kitn-ai.github.io/registry](https://kitn-ai.github.io/registry)**

## Registered Registries

| Namespace | Description | Homepage |
|-----------|-------------|----------|
| `@kitn` | Official kitn AI agent components | [kitn.ai](https://kitn.ai) |

## Register Your Own Registry

Anyone can host a kitn-compatible component registry. To add yours to the public directory:

### 1. Host your registry

Your registry needs to serve two things:

- **Registry index** at a stable URL — a JSON file listing all components (see [schema](#registry-schema))
- **Component JSON files** — individual files with source code for each component

The URL template uses `{type}` and `{name}` placeholders:

```
https://your-domain.com/r/{type}/{name}.json
```

For example, `https://your-domain.com/r/agents/weather-agent.json`

### 2. Open a PR

Add your registry to `registries.json`:

```json
{
  "name": "@yourteam",
  "url": "https://your-domain.com/r/{type}/{name}.json",
  "homepage": "https://your-domain.com",
  "description": "Short description of your components"
}
```

### 3. Users add your registry

Once listed, users can add your registry to their project:

```bash
kitn registry add @yourteam https://your-domain.com/r/{type}/{name}.json \
  --homepage https://your-domain.com \
  --description "Short description"
```

Then install components:

```bash
kitn add @yourteam/component-name
```

## Registry Schema

### Registry Index (`registry.json`)

```json
{
  "version": "1.0.0",
  "items": [
    {
      "name": "weather-agent",
      "type": "kitn:agent",
      "description": "Weather specialist agent",
      "registryDependencies": ["weather-tool"],
      "categories": ["weather", "api"],
      "version": "1.0.0"
    }
  ]
}
```

### Component JSON (`{type}/{name}.json`)

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Unique component identifier |
| `type` | Yes | One of `kitn:agent`, `kitn:tool`, `kitn:skill`, `kitn:storage`, `kitn:package` |
| `description` | Yes | Short description |
| `files` | Yes | Array of `{ path, content, type }` with full source code |
| `dependencies` | No | npm dependencies |
| `devDependencies` | No | npm dev dependencies |
| `registryDependencies` | No | Other kitn components this depends on |
| `envVars` | No | Required environment variables |
| `docs` | No | Post-install instructions |
| `categories` | No | Tags for filtering |
| `version` | No | Semver version (default `1.0.0`) |

## Building Components

Component source and build tooling lives in the [kitn monorepo](https://github.com/kitn-ai/kitn). The built output (`r/` directory) is deployed here via GitHub Pages.

## Links

- [kitn monorepo](https://github.com/kitn-ai/kitn) — framework, CLI, and component source
- [kitn.ai](https://kitn.ai) — project homepage
- [npm: @kitnai/cli](https://www.npmjs.com/package/@kitnai/cli) — the CLI
