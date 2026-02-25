---
name: create-component
description: Use when the user asks to create a new registry component (agent, tool, skill, or storage)
---

# Create a New Registry Component

## Step 1: Gather Information

Ask the user for these details (suggest defaults where possible):

| Field | Required | Notes |
|-------|----------|-------|
| **type** | Yes | `agent`, `tool`, `skill`, or `storage` |
| **name** | Yes | kebab-case, no type suffix (e.g., `weather` not `weather-tool`) |
| **description** | Yes | One-line description |
| **dependencies** | No | npm packages beyond defaults |
| **registryDependencies** | No | Other kitn components this depends on |
| **envVars** | No | Object of `{ VAR_NAME: "description" }` |
| **categories** | No | Tags for discovery |

## Step 2: Scaffold by Type

### Agent: `components/agents/<name>-agent/`

**Files to create:**

1. `components/agents/<name>-agent/manifest.json`
2. `components/agents/<name>-agent/<name>-agent.ts`

**manifest.json template:**
```json
{
  "name": "<name>-agent",
  "type": "kitn:agent",
  "description": "<description>",
  "dependencies": ["ai"],
  "registryDependencies": [],
  "envVars": {},
  "files": ["<name>-agent.ts"],
  "docs": "<post-install instructions>",
  "categories": [],
  "changelog": [
    { "version": "1.0.0", "date": "<YYYY-MM-DD>", "type": "initial", "note": "Initial release" }
  ]
}
```

**Source file template (`<name>-agent.ts`):**
```typescript
const SYSTEM_PROMPT = `You are a <description> agent.

<instructions for the agent>`;

export const <UPPER_SNAKE_NAME>_AGENT_CONFIG = {
  system: SYSTEM_PROMPT,
  tools: {},
};
```

If the agent uses tools from registryDependencies, import them:
```typescript
import { someTool } from "@kitn/tools/<toolfile>.js";
```

---

### Tool: `components/tools/<name>-tool/`

**CRITICAL: The source file drops the `-tool` suffix.**

**Files to create:**

1. `components/tools/<name>-tool/manifest.json`
2. `components/tools/<name>-tool/<name>.ts` (NOT `<name>-tool.ts`)

**manifest.json template:**
```json
{
  "name": "<name>-tool",
  "type": "kitn:tool",
  "description": "<description>",
  "dependencies": ["ai", "zod"],
  "registryDependencies": [],
  "envVars": {},
  "files": ["<name>.ts"],
  "docs": "<post-install instructions>",
  "categories": [],
  "changelog": [
    { "version": "1.0.0", "date": "<YYYY-MM-DD>", "type": "initial", "note": "Initial release" }
  ]
}
```

**Source file template (`<name>.ts`):**
```typescript
import { tool } from "ai";
import { z } from "zod";

export const <camelCaseName>Tool = tool({
  description: "<tool description>",
  inputSchema: z.object({
    // define input parameters
  }),
  execute: async (input) => {
    // implement tool logic
  },
});
```

---

### Skill: `components/skills/<name>/`

**Files to create:**

1. `components/skills/<name>/manifest.json`
2. `components/skills/<name>/README.md`

**manifest.json template:**
```json
{
  "name": "<name>",
  "type": "kitn:skill",
  "description": "<description>",
  "dependencies": [],
  "registryDependencies": [],
  "envVars": {},
  "files": ["README.md"],
  "docs": "<post-install instructions>",
  "categories": [],
  "changelog": [
    { "version": "1.0.0", "date": "<YYYY-MM-DD>", "type": "initial", "note": "Initial release" }
  ]
}
```

**README.md template:**
```markdown
---
name: <name>
description: <when to activate this skill>
tags: [<relevant>, <tags>]
phase: <response|analysis|planning>
---

# <Display Name>

## When to Use

- <trigger condition 1>
- <trigger condition 2>

## Instructions

1. **<Step 1>** -- <details>
2. **<Step 2>** -- <details>
```

---

### Storage: `components/storage/<name>-store/`

**Files to create:**

1. `components/storage/<name>-store/manifest.json`
2. `components/storage/<name>-store/<name>-store.ts`

**manifest.json template:**
```json
{
  "name": "<name>-store",
  "type": "kitn:storage",
  "description": "<description>",
  "dependencies": [],
  "registryDependencies": [],
  "envVars": {},
  "files": ["<name>-store.ts"],
  "docs": "<post-install instructions>",
  "categories": [],
  "changelog": [
    { "version": "1.0.0", "date": "<YYYY-MM-DD>", "type": "initial", "note": "Initial release" }
  ]
}
```

**Source file template (`<name>-store.ts`):**
```typescript
import { readFile, writeFile, mkdir } from "node:fs/promises";
import { existsSync } from "node:fs";
import { join } from "node:path";

export interface <Name>Store {
  // define interface methods
}

export function create<Name>Store(dataDir: string): <Name>Store {
  const dir = join(dataDir, "<name>");

  async function ensureDir() {
    if (!existsSync(dir)) await mkdir(dir, { recursive: true });
  }

  return {
    // implement interface methods
  };
}
```

## Step 3: Validate and Build

After creating the files, run:

```bash
bun run validate    # Check imports and dependencies
bun run build       # Generate r/ output
bun test            # Run schema tests
```

Fix any errors before proceeding.

## Step 4: Commit

```bash
git add components/<type>/<directory>/ r/
git commit -m "feat: add <name> <type>"
```

## Checklist

- [ ] Directory and file names follow naming conventions (especially tool file naming)
- [ ] manifest.json has all required fields
- [ ] `files` array in manifest matches actual file names
- [ ] Dependencies are correct (`ai` for agents, `ai` + `zod` for tools)
- [ ] registryDependencies lists any imported kitn components
- [ ] Imports use `@kitn/<type>/<file>.js` for cross-type, relative for same-directory
- [ ] All imports have `.js` extension
- [ ] Changelog has initial entry with today's date
- [ ] `bun run validate` passes
- [ ] `bun run build` passes
- [ ] `bun test` passes
- [ ] Built output in `r/` is committed
