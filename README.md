# GitHub Actions

A collection of reusable GitHub Actions for SF Studios.

## install-node-modules

Installs Node.js dependencies with automatic package manager detection and caching.

**Supports:** npm, yarn, and bun.

### How it works

1. Optionally checks out the repository (if `branch` is provided)
2. Auto-detects the package manager from the lock file:
   - `bun.lockb` or `bun.lock` → **bun**
   - `yarn.lock` → **yarn**
   - `package-lock.json` → **npm**
3. Sets up the runtime ([actions/setup-node](https://github.com/actions/setup-node) and/or [oven-sh/setup-bun](https://github.com/oven-sh/setup-bun)) with built-in caching and `@sfstudios` registry auth
4. Runs the appropriate install command (`npm ci`, `yarn install --frozen-lockfile`, or `bun install --frozen-lockfile`)

### Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `NPM_TOKEN` | yes | — | Token for private GitHub Packages registry |
| `branch` | no | `""` | Git ref to checkout. Leave empty to skip checkout. |
| `package-manager` | no | `"auto"` | Force `npm`, `yarn`, `bun`, or `auto` (detect from lock file) |
| `node-version-file` | no | `".nvmrc"` | File to read Node.js version from |

### Usage

#### Auto-detect (recommended)

The action detects the package manager from the lock file in your repository:

```yaml
- uses: sfstudios/actions/install-node-modules@master
  with:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
    branch: ${{ github.ref }}
```

#### Without checkout

If you handle checkout yourself, omit `branch`:

```yaml
- uses: actions/checkout@v6

- uses: sfstudios/actions/install-node-modules@master
  with:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

#### Force a specific package manager

```yaml
- uses: sfstudios/actions/install-node-modules@master
  with:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
    branch: ${{ github.ref }}
    package-manager: bun
```

## npm-install-node-modules (legacy wrapper)

Backwards-compatible wrapper that calls `install-node-modules` with `package-manager: npm`.

Existing workflows using this action will continue to work without changes.

```yaml
- uses: sfstudios/actions/npm-install-node-modules@master
  with:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
    branch: ${{ github.ref }}
```

## Migration from previous versions

If you were using the old `install-node-modules` action with yarn, **no changes are needed** — the action auto-detects `yarn.lock` and behaves the same way.

If you were using `npm-install-node-modules`, **no changes are needed** — the wrapper forwards to the unified action.

### What changed

- Caching is now handled by `actions/setup-node` built-in cache (global package cache instead of `node_modules`)
- Registry auth is handled by `actions/setup-node` built-in `registry-url` + `NODE_AUTH_TOKEN` (no more manual `.npmrc` manipulation)
- All action dependencies updated to latest versions (`checkout@v6`, `setup-node@v6`, `setup-bun@v2`)
- Bun support added via `oven-sh/setup-bun@v2`
