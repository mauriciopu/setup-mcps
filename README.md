# setup-mcps

Claude Code skill that installs and verifies all MCP servers used in wagent-ai development.

## Install

```bash
npx skills add mauriciopu/setup-mcps
```

## Usage

```
/setup-mcps              # check status of all MCPs
/setup-mcps --install    # install missing MCPs
/setup-mcps --reinstall  # reinstall all MCPs
```

## MCPs covered

| MCP | Purpose |
|-----|---------|
| `context7` | Real-time library docs |
| `excalidraw` | Architecture diagrams |
| `filesystem` | Local filesystem access |
| `github` | PRs, issues, code search |
| `granola` | Meeting transcripts |
| `linear` | Task management |
| `mermaid` | Mermaid diagram generation |
| `playwright` | E2E testing & web automation |
| `pencil` | UI/UX design (manual install) |
