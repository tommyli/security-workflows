# security-workflows

Reusable GitHub Actions workflows for Tommy Li's repositories. Public so private repositories under other owners
(`firefire-au`) can call it; it holds no secrets.

## secret-scan

Runs [Betterleaks](https://github.com/betterleaks/betterleaks) 1.9.0 (checksum-verified, validation off) on the
pushed or pull-request range, or over full history when `full-history` is true. The rule set is the caller's own
`.betterleaks.toml` / `.gitleaks.toml` when present, otherwise the personal rule set embedded in the workflow (a copy
of `tommyli/ai-agents` `config/betterleaks.toml`). Findings print with values redacted.

Caller, `.github/workflows/secret-scan.yml`, pinned by commit SHA:

```yaml
name: secret-scan
on:
  push:
  pull_request:
  schedule:
    - cron: "17 3 * * 1"
  workflow_dispatch:
permissions:
  contents: read
jobs:
  scan:
    uses: tommyli/security-workflows/.github/workflows/secret-scan.yml@<commit-sha>  # v1.0.0
    with:
      full-history: ${{ github.event_name == 'schedule' || github.event_name == 'workflow_dispatch' }}
```

Design: `tommyli/ai-agents` `docs/secrets-detection/`. To change the scanner version, update `BL_VERSION` and
`BL_SHA256` from the release's `checksums.txt`; before Betterleaks v2, read that plan's step 18.
