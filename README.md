# depx

**Understand what's in your node_modules.**

A fast, intelligent dependency analyzer for JavaScript/TypeScript projects. Built in Rust for performance.

## Why depx?

Your `node_modules` has hundreds of packages. Do you know:
- Which ones are actually imported in your code?
- Why `is-odd` is even installed?
- If that vulnerability alert affects code you actually use?

Existing tools (`npm ls`, `npm audit`, `depcheck`) give fragmented information without real context. depx connects the dots.

## Installation

```bash
cargo install depx
```

## Commands

### `depx analyze` - Find unused dependencies

```bash
$ depx analyze

Dependency Analysis Report

Summary
  227 packages used
  6 dev/build tools (expected, not imported)

Dev/Build Tools (not imported, expected):
  ~ @types/node@20.19.24
  ~ @typescript-eslint/eslint-plugin@7.18.0
  ~ ts-node@10.9.2
```

Smart detection separates truly unused packages from dev/build tools that aren't meant to be imported (`@types/*`, `typescript`, `eslint`, `vitest`, etc).

### `depx why <package>` - Explain why a package is installed

```bash
$ depx why esbuild

Package: esbuild@0.21.5

Dependency chains:
  -> vite -> esbuild

  Note: This package is only required for development
```

Shows the full dependency chain from your `package.json` to any transitive dependency.

### `depx audit` - Check for real vulnerabilities

```bash
$ depx audit

2 vulnerabilities found

CRITICAL
  GHSA-xvch-5gv4-984h minimist@1.2.5 - Prototype Pollution [USED]
       Fix: 1.2.5 -> 1.2.6

MEDIUM
  GHSA-9c47-m6qq-7p4h json5@2.2.1 - Prototype Pollution in JSON5 [USED]
       Fix: 2.2.1 -> 2.2.2
```

Unlike `npm audit`, depx queries the OSV database **with your exact installed versions**, eliminating false positives from old CVEs that don't affect you.

### `depx deprecated` - Find deprecated packages

```bash
$ depx deprecated

4 deprecated packages found

  - eslint@8.57.1 [unused]
    This version is no longer supported.
  - inflight@1.0.6 [unused]
    This module is not supported, and leaks memory.
```

## Features

- **Fast** - Written in Rust, parses JS/TS with [oxc](https://oxc.rs)
- **Smart** - Distinguishes real unused deps from expected dev tools
- **Accurate** - Version-aware vulnerability scanning (no false positives)
- **Complete** - Handles ES modules, CommonJS, dynamic imports, re-exports

## Supported lockfiles

- [x] `package-lock.json` (npm)
- [ ] `pnpm-lock.yaml` (coming soon)
- [ ] `yarn.lock` (coming soon)

## License

MIT
