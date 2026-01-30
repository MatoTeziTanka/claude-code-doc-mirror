# Claude Code Doc Mirror

A lightweight bash tool that mirrors the official [Claude Code documentation](https://code.claude.com/docs) locally, with automatic change detection, changelog generation, and optional knowledge-graph integration.

**GitHub:** https://github.com/MatoTeziTanka/claude-code-doc-mirror

## Workflow

```
Local storage (edit here)  -->  GitHub (push)  -->  Production deployment (pull from GitHub)
      canonical                  mirror/distribution          runtime deployment
```

1. All edits are made on local storage at `[YOUR_BASE_PATH]/doc-mirror/`
2. Push to GitHub: `git push origin main`
3. Your production deployment pulls from GitHub to refresh at the desired location

The production deployment is a **deployment target only**. It does not author changes.

## Why This Exists

- **Offline Access**: Read Claude Code docs without internet
- **Change Tracking**: Know when Anthropic updates their documentation
- **Version History**: Maintain a changelog of documentation changes
- **LLM Context**: Feed complete, up-to-date docs into your AI workflows
- **Graph Integration**: Optionally record every change in your knowledge graph

## Features

- Mirrors all 51 pages of Claude Code documentation
- SHA-256 hash-based change detection
- Auto-generated CHANGELOG.md with timestamps
- Optional knowledge-graph integration (per-file change nodes + sync run summaries)
- Minimal dependencies (curl, sha256sum, bash)
- Cron-friendly for scheduled syncs
- Zero external services required

## Setup

```bash
# Clone the repository
git clone https://github.com/MatoTeziTanka/claude-code-doc-mirror.git doc-mirror
cd doc-mirror

# Ensure sync script is executable
chmod +x bin/claude-docs-sync.sh

# Create runtime directories
mkdir -p data/claude-code/en logs

# Set your base path (see usage below)
export DOC_MIRROR_BASE="$(pwd)"
```

## Usage

### Manual Sync

```bash
# Run a sync (fetches all 51 docs, detects changes)
export DOC_MIRROR_BASE="$(pwd)"
./bin/claude-docs-sync.sh
```

### Scheduled Sync (Cron)

```bash
# Twice-daily updates (7am and 7pm EST)
0 7,19 * * * DOC_MIRROR_BASE=/path/to/doc-mirror /path/to/doc-mirror/bin/claude-docs-sync.sh >> /path/to/doc-mirror/logs/cron.log 2>&1
```

### View Changes

```bash
# Check the changelog
cat data/claude-code/CHANGELOG.md

# View hash manifest
cat data/claude-code/hashes.manifest

# Query your graph for doc changes (if graph integration is enabled)
grep "doc-mirror-change" "$GRAPH"

# Query your graph for sync runs
grep "doc-sync-run" "$GRAPH"
```

## Directory Structure

```
doc-mirror/
├── bin/
│   └── claude-docs-sync.sh    # Main sync script (optionally graph-aware)
├── config/
│   └── sources.yaml           # Source configuration (extensible)
├── data/
│   └── claude-code/
│       ├── en/                # Mirrored markdown files (51 docs, gitignored)
│       ├── hashes.manifest    # SHA-256 hashes (gitignored)
│       └── CHANGELOG.md       # Auto-generated change log (tracked)
├── logs/                      # Sync logs (gitignored)
├── .github/
│   └── FUNDING.yml
├── .gitignore
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## Requirements

- Bash 4.0+
- curl
- sha256sum (coreutils)
- Standard POSIX utilities (mktemp, date, tee)

## License

MIT License - See [LICENSE](LICENSE) for details.

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

*Developed by [Mato Tezi Tanka](https://github.com/MatoTeziTanka)*
