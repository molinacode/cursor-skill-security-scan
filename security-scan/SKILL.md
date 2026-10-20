---
name: security-scan
description: >-
  Audit agent configuration (hooks, MCP, rules, skills, secrets) with
  AgentShield (ECC). Use after changing hooks/MCP, when onboarding a repo, or
  when asked to review Cursor/Claude agent setup security — not application
  code (use review-security for code diffs).
metadata:
  origin: ECC
  adapted-for: cursor
  license: MIT
---

# Security Scan (AgentShield)

Audits **agent configuration**, not application vulnerabilities.
Tool: [AgentShield](https://github.com/affaan-m/agentshield) (`ecc-agentshield`).

## When to activate

- After editing `hooks.json`, hooks, MCP, rules, or skills
- When opening / onboarding a repo with `.cursor/` or `.claude/`
- Periodic agent-setup hygiene checks
- Before sharing or publishing agent configs

## Do not confuse with

| This | That |
|------|------|
| **security-scan** → agent config (hooks, MCP, secrets in configs) | **review-security** → application code diffs |

## What to scan in Cursor (order)

Do **not** pass `--path` to your entire `~/.cursor` / `%USERPROFILE%\.cursor` tree: it includes VS Code/Cursor extensions and creates noise.

1. **User global hooks**
   ```bash
   # Windows
   npx ecc-agentshield scan --path "%USERPROFILE%\.cursor\hooks" --format markdown --min-severity medium

   # macOS / Linux
   npx ecc-agentshield scan --path "$HOME/.cursor/hooks" --format markdown --min-severity medium
   ```
   If needed, also review `hooks.json` and scripts under `~/.cursor/hooks/` by hand.

2. **Current project config** (preferred day-to-day)
   ```bash
   npx ecc-agentshield scan --path ".cursor" --format markdown --min-severity medium
   ```
   If the project uses Claude Code:
   ```bash
   npx ecc-agentshield scan --path ".claude" --format markdown --min-severity medium
   ```

3. **Project MCP** (if outside `.cursor`)
   ```bash
   npx ecc-agentshield scan --path "." --format markdown --min-severity medium
   ```
   Then filter findings to `mcp.json`, `.mcp.json`, hooks, and rules; ignore `node_modules` and build output (AgentShield usually skips them).

## Useful commands

```bash
npx ecc-agentshield --version
npx ecc-agentshield scan --path ".cursor"
npx ecc-agentshield scan --path ".cursor" --format json
npx ecc-agentshield scan --path ".cursor" --min-severity medium

# Auto-fix ONLY if the user explicitly asks (safe fixes only)
npx ecc-agentshield scan --path ".cursor" --fix
```

Optional global install: `npm install -g ecc-agentshield`.

## Interpretation checklist

Prioritize in this order:

1. Hardcoded secrets (API keys, tokens) in configs
2. Hooks with injection (`${file}`, remote shell, exfiltration)
3. MCP with shell, broad filesystem, or unpinned `npx -y`
4. Overly broad permissions / bypass flags
5. Auto-run instructions or prompt-injection surface in rules/agents

For each critical/high: path, severity, why it matters, concrete remediation, whether auto-fixable.

Separate **active runtime** findings (real `hooks.json`, live MCP) from template/extension noise.

## Output contract

1. Grade/score if the scanner provides one
2. Counts by severity
3. Critical/high with paths
4. Remaining findings grouped (or discarded if extension/template noise)
5. Remediation order
6. Commands run

## Auto-fix and Opus

- `--fix`: only with explicit user permission; summarize the plan before applying
- `--opus` / `--deep`: require an API key; do not run by default

## Origin / attribution

Cherry-pick of [ECC](https://github.com/affaan-m/ECC) `skills/security-scan`, adapted for Cursor paths.
Does **not** install the full ECC harness. Scanner: [AgentShield](https://github.com/affaan-m/agentshield).
Both upstream projects are MIT-licensed; this adaptation is also MIT.
