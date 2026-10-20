# cursor-skill-security-scan

Cursor Agent Skill that audits **agent configuration** (hooks, MCP, rules, skills, secrets) with [AgentShield](https://github.com/affaan-m/agentshield) from the [ECC](https://github.com/affaan-m/ECC) ecosystem.

It does **not** replace application code security review. For code diffs in Cursor, use the built-in `review-security` flow.

## Install (Cursor)

### Personal (all projects)

```bash
git clone https://github.com/molinacode/cursor-skill-security-scan.git
mkdir -p ~/.cursor/skills
cp -r cursor-skill-security-scan/security-scan ~/.cursor/skills/security-scan
```

Windows (PowerShell):

```powershell
git clone https://github.com/molinacode/cursor-skill-security-scan.git
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.cursor\skills" | Out-Null
Copy-Item -Recurse cursor-skill-security-scan\security-scan "$env:USERPROFILE\.cursor\skills\security-scan"
```

### Project-only

Copy `security-scan/` into `.cursor/skills/security-scan/` in the repo.

### One-liner (skills CLI, if you use it)

```bash
npx skills add molinacode/cursor-skill-security-scan --skill security-scan -g
```

(Exact CLI flags may vary by tools version; the manual copy above always works.)

## Use

In Cursor Agent chat:

- “Run a security-scan on this project’s `.cursor`”
- “Audit my agent hooks/MCP setup”

Requires Node.js 18+. The skill runs AgentShield via:

```bash
npx ecc-agentshield scan --path ".cursor" --min-severity medium
```

## What it scans / what it does not

| Scans | Does not scan |
|-------|----------------|
| Hooks, MCP configs, agent rules/skills | App source for XSS/SQLi/etc. |
| Secrets and dangerous flags in agent config | Full monorepo blindly (noisy) |

Prefer scanning `.cursor` or `.claude` per project — not the entire home `~/.cursor` tree (extensions create false positives).

## Attribution

Adapted from ECC’s `skills/security-scan` ([affaan-m/ECC](https://github.com/affaan-m/ECC)), MIT.
Scanner: [affaan-m/agentshield](https://github.com/affaan-m/agentshield) (`ecc-agentshield`), MIT.

This repo is an independent Cursor-oriented packaging. Not affiliated with or endorsed by the ECC maintainers.

## License

MIT — see [LICENSE](LICENSE).
