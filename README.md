# depx

[![Crates.io](https://img.shields.io/crates/v/depx.svg)](https://crates.io/crates/depx)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Understand what's in your node_modules and Cargo.lock.**

<p align="center">
  <img src="demo.gif" alt="depx demo" width="600">
</p>

A fast, intelligent dependency analyzer for JavaScript/TypeScript and Rust projects. Built in Rust for performance.

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

### `depx duplicates` - Detect duplicate dependencies (Rust/Cargo)

```bash
$ depx duplicates

Duplicate Dependencies Analysis

Summary
  14 crates with multiple versions
  1 high severity (3+ versions)
  2 medium severity (different major versions)
  11 low severity (same major version)
  16 extra compile units

HIGH SEVERITY
  ! windows-sys (4 versions)
      v0.52.0 ← ring
      v0.59.0 ← colored
      v0.60.2 ← socket2, terminal_size
      v0.61.2 ← anstyle-query, anstyle-wincon +7 more

MEDIUM SEVERITY
  ~ thiserror (2 versions)
      v1.0.69 ← oxc-miette
      v2.0.17 ← depx
```

Identifies when multiple versions of the same crate exist in your project, calculates the impact (extra compile units), and suggests which dependencies to update.

**Options:**
- `--verbose` / `-v` - Show all duplicates including low severity, with upgrade suggestions
- `--json` - Output as JSON for programmatic use

## Features

- **Fast** - Written in Rust, parses JS/TS with [oxc](https://oxc.rs)
- **Smart** - Distinguishes real unused deps from expected dev tools
- **Accurate** - Version-aware vulnerability scanning (no false positives)
- **Complete** - Handles ES modules, CommonJS, dynamic imports, re-exports

## Supported lockfiles

- [x] `Cargo.lock` (Rust) - duplicates detection
- [x] `package-lock.json` (npm) - full analysis
- [ ] `pnpm-lock.yaml` (coming soon)
- [ ] `yarn.lock` (coming soon)

## Built with AI

This project was built in partnership with Claude (Anthropic). I define the architecture, make decisions, review code, and handle the direction. Claude helps write code faster.

I believe AI is a tool, not a replacement. The developer still needs to understand the problem, evaluate solutions, and take responsibility for the result. AI just accelerates execution.

You can see Claude as a contributor in this repo, that's intentional transparency.

## License

MIT
