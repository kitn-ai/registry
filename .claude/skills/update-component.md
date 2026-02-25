---
name: update-component
description: Use when the user asks to modify, update, fix, or improve an existing registry component
---

# Update an Existing Component

## Step 1: Identify the Component

Find the component to modify:

```bash
ls components/<type>/<name>/
```

Read both the manifest and source files to understand the current state:

1. `components/<type>/<name>/manifest.json` -- current metadata and version
2. Source files listed in the manifest's `files` array

## Step 2: Make Source Changes

Follow existing patterns in the component. Key conventions:

- **Agents**: Export `UPPER_SNAKE_AGENT_CONFIG = { system, tools }`
- **Tools**: Export `camelCaseTool = tool({ description, inputSchema, execute })`
- **Skills**: Markdown with YAML frontmatter (name, description, tags, phase)
- **Storage**: Export `create<Name>Store(dataDir)` factory function

Import rules:
- Cross-type: `@kitn/<type>/<file>.js` (e.g., `@kitn/tools/weather.js`)
- Same-directory: relative paths (e.g., `./utils.js`)
- Always include `.js` extension

## Step 3: Bump Version

Determine the version bump based on the change:

| Change Type | Bump | Commit Prefix | Changelog Type |
|-------------|------|---------------|----------------|
| Bug fix, typo, minor correction | Patch (1.0.0 -> 1.0.1) | `fix:` | `fix` |
| New feature, capability addition | Minor (1.0.0 -> 1.1.0) | `feat:` | `feature` |
| Breaking change, API change | Major (1.0.0 -> 2.0.0) | `feat!:` | `breaking` |

Update the version in `manifest.json` and add a changelog entry to the **beginning** of the changelog array:

```json
{
  "changelog": [
    { "version": "<new-version>", "date": "<YYYY-MM-DD>", "type": "<type>", "note": "<what changed>" },
    { "version": "1.0.0", "date": "2026-02-25", "type": "initial", "note": "Initial release" }
  ]
}
```

## Step 4: Update Manifest Fields

Check if any other manifest fields need updating:

- **dependencies** -- added or removed npm packages?
- **registryDependencies** -- added or removed kitn component imports?
- **envVars** -- new environment variables needed?
- **description** -- does it still accurately describe the component?
- **docs** -- do post-install instructions need updating?
- **categories** -- any new relevant tags?
- **files** -- added or removed source files?

## Step 5: Validate and Build

```bash
bun run validate    # Check imports and dependencies
bun run build       # Regenerate r/ output
bun test            # Run schema tests
```

Fix any errors before proceeding.

## Step 6: Commit

```bash
git add components/<type>/<name>/ r/
git commit -m "<prefix>(<component-name>): <short description>"
```

Examples:
- `fix(weather-tool): handle missing geocoding results`
- `feat(memory-store): add bulk delete operation`
- `feat!(web-search-tool): change response format to include metadata`

## Checklist

- [ ] Source changes follow existing patterns in the component
- [ ] Version bumped appropriately (patch/minor/major)
- [ ] Changelog entry added to beginning of array with today's date
- [ ] Changelog `type` matches the change (fix/feature/breaking)
- [ ] manifest.json fields updated if needed (deps, registryDeps, envVars, files)
- [ ] Imports use correct conventions (`@kitn/` for cross-type, `.js` extensions)
- [ ] `bun run validate` passes
- [ ] `bun run build` passes
- [ ] `bun test` passes
- [ ] Built output in `r/` is committed
