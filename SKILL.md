---
name: setup-mcps
description: Instala y verifica todos los MCP servers y skills usados en wagent-ai (context7, filesystem, github, granola, linear, mermaid, pencil, playwright, sqlserver, MS 365, Atlassian + skills superpowers y ui-ux-pro-max)
argument-hint: "--check | --install | --reinstall"
---

# Setup MCPs & Skills — wagent-ai

Instala y verifica los MCP servers y skills requeridos para desarrollo en wagent-ai.

## MCPs requeridos por este proyecto

### MCPs CLI (instalables via `claude mcp add`)

| MCP | Scope | Propósito | Comando |
|-----|-------|-----------|---------|
| `context7` | user | Docs de librerías en tiempo real | `npx -y @upstash/context7-mcp@latest` |
| `filesystem` | user | Acceso al filesystem local | `cmd /c npx -y @modelcontextprotocol/server-filesystem D:/Usuarios/mpuyo/Escritorio` |
| `github` | user | PRs, issues, code search en GitHub | `cmd /c npx -y @modelcontextprotocol/server-github` + `GITHUB_PERSONAL_ACCESS_TOKEN` |
| `mermaid` | user | Generación de diagramas Mermaid | `npx -y @peng-shawn/mermaid-mcp-server` |
| `playwright` | user | Testing E2E y web scraping/documentación | `cmd /c npx -y @playwright/mcp` |
| `excalidraw` | user | Diagramas colaborativos | `cmd /c npx -y excalidraw-mcp` |
| `sqlserver` | user | Queries SQL Server directas | `npx -y @galileicloud/mcp-sqlserver` + env vars DB |

### MCPs URL (HTTP remoto)

| MCP | Scope | Propósito | URL |
|-----|-------|-----------|-----|
| `granola` | user | Transcripts de reuniones | `https://mcp.granola.ai/mcp` |
| `linear-server` | user | Gestión de tareas en Linear | `https://mcp.linear.app/mcp` |

### MCPs Globales (claude.ai Integrations)

| MCP | Propósito | Cómo activar |
|-----|-----------|--------------|
| `Microsoft 365` | Email, calendario, SharePoint | claude.ai > Settings > Integrations > Microsoft 365 |
| `Atlassian` | Jira, Confluence | claude.ai > Settings > Integrations > Atlassian |
| `pencil` | Diseño de pantallas UI/UX | VS Code Extension: Pencil Dev |

> Los MCPs globales se configuran desde claude.ai o extensiones de VS Code, no via CLI.

## Skills requeridas por este proyecto

| Skill Pack | Repo | Propósito |
|------------|------|-----------|
| `superpowers` | `obra/superpowers` | TDD, debugging sistemático, plan execution, worktrees, code review, subagentes |
| `ui-ux-pro-max` | `nextlevelbuilder/ui-ux-pro-max-skill` | Design system generator, 67 estilos UI, 161 industry rules (incluye Hotel), shadcn/ui + Tailwind |

## Argumentos

- `--check` (default): solo muestra el estado de cada MCP y skill, sin instalar nada
- `--install`: instala los MCPs y skills que faltan
- `--reinstall`: reinstala todos aunque ya estén instalados

## Ejecución

### Paso 1 — Revisar estado actual

Ejecuta `claude mcp list` y captura la salida. Identifica qué MCPs de las tablas de arriba ya están instalados y cuáles faltan.

Para skills, verifica si existen los directorios en `.claude/skills/`:
- `.claude/skills/test-driven-development/` (de superpowers)
- `.claude/skills/ui-ux-pro-max/` (de ui-ux-pro-max-skill)

Presenta un resumen en tabla:

| Componente | Tipo | Estado | Notas |
|------------|------|--------|-------|
| context7 | MCP CLI | ✅ / ❌ / ⚠️ | ... |
| granola | MCP URL | ✅ / ❌ | ... |
| Microsoft 365 | MCP Global | ✅ / ❌ | ... |
| superpowers | Skill Pack | ✅ / ❌ | ... |
| ... | ... | ... | ... |

### Paso 2 — Instalar MCPs CLI faltantes (solo si `--install` o `--reinstall`)

Para cada MCP faltante, ejecuta el comando correspondiente en orden:

#### context7
```bash
claude mcp add context7 -s user -- npx -y @upstash/context7-mcp@latest
```

#### filesystem
```bash
claude mcp add filesystem -s user -- cmd /c "npx -y @modelcontextprotocol/server-filesystem D:/Usuarios/mpuyo/Escritorio"
```

#### github
Requiere `GITHUB_PERSONAL_ACCESS_TOKEN`. Verifica si la variable de entorno existe antes de instalar.

```bash
claude mcp add github -s user \
  -e GITHUB_PERSONAL_ACCESS_TOKEN="${GITHUB_PERSONAL_ACCESS_TOKEN}" \
  -- cmd /c "npx -y @modelcontextprotocol/server-github"
```

Si la variable no está definida, avisa al usuario y salta este MCP.

#### mermaid
```bash
claude mcp add mermaid -s user -- npx -y @peng-shawn/mermaid-mcp-server
```

#### playwright
```bash
claude mcp add playwright -s user -- cmd /c "npx -y @playwright/mcp"
```

> Si es la primera vez, puede requerir: `npx playwright install` para descargar los browsers.

#### excalidraw
```bash
claude mcp add excalidraw -s user -- cmd /c "npx -y excalidraw-mcp"
```

#### sqlserver
Requiere variables de entorno de conexión a la base de datos.

```bash
claude mcp add sqlserver -s user \
  -e DB_SERVER="${DB_SERVER}" \
  -e DB_PORT="${DB_PORT:-1433}" \
  -e DB_DATABASE="${DB_DATABASE}" \
  -e DB_USER="${DB_USER}" \
  -e DB_PASSWORD="${DB_PASSWORD}" \
  -e DB_ENCRYPT="${DB_ENCRYPT:-false}" \
  -e DB_TRUST_SERVER_CERT="${DB_TRUST_SERVER_CERT:-true}" \
  -- npx -y @galileicloud/mcp-sqlserver
```

Si las variables no están definidas, avisa al usuario:
```
⚠️  Variables de conexión SQL Server no encontradas.
    Configura: DB_SERVER, DB_DATABASE, DB_USER, DB_PASSWORD
    Luego vuelve a correr /setup-mcps --install
```

### Paso 3 — Instalar MCPs URL faltantes (solo si `--install` o `--reinstall`)

#### granola
```bash
claude mcp add granola -s user --url "https://mcp.granola.ai/mcp"
```

> Puede requerir autenticación en el browser la primera vez.

#### linear-server
```bash
claude mcp add linear-server -s user --url "https://mcp.linear.app/mcp"
```

> Requiere autenticación OAuth en el browser la primera vez.

### Paso 4 — MCPs Globales (acción manual)

Estos no se pueden instalar via CLI. Informa al usuario:

```
ℹ️  MCPs globales — requieren configuración manual:

  Microsoft 365  → claude.ai > Settings > Integrations > Microsoft 365
  Atlassian      → claude.ai > Settings > Integrations > Atlassian
  pencil         → Instalar extensión VS Code: Pencil Dev (highagency.pencildev)
```

### Paso 5 — Instalar Skills faltantes (solo si `--install` o `--reinstall`)

#### superpowers (obra/superpowers)

Incluye 13 skills: TDD, systematic-debugging, writing-plans, executing-plans, finishing-a-development-branch, verification-before-completion, brainstorming, requesting-code-review, receiving-code-review, using-git-worktrees, subagent-driven-development, writing-skills, using-superpowers.

```bash
claude skills add obra/superpowers
```

#### ui-ux-pro-max (nextlevelbuilder/ui-ux-pro-max-skill)

Design system generator con 67 estilos UI, 161 industry rules, 161 paletas de color, 57 font pairings, 25 chart types. Soporta shadcn/ui, Next.js, React, Tailwind.

```bash
claude skills add nextlevelbuilder/ui-ux-pro-max-skill
```

> Requiere Python 3 para el script de búsqueda de design systems.

### Paso 6 — Verificar instalación

Después de instalar, ejecuta `claude mcp list` de nuevo y verifica que todos los MCPs aparecen conectados. Para skills, confirma que los directorios existen en `.claude/skills/`.

### Paso 7 — Resumen final

Presenta tabla final con el estado post-instalación:

```
✅ MCPs CLI instalados:
  - context7          (@upstash/context7-mcp)
  - filesystem         (@modelcontextprotocol/server-filesystem)
  - github             (@modelcontextprotocol/server-github)
  - mermaid            (@peng-shawn/mermaid-mcp-server)
  - playwright         (@playwright/mcp)
  - excalidraw         (excalidraw-mcp)
  - sqlserver           (@galileicloud/mcp-sqlserver)

✅ MCPs URL instalados:
  - granola            (https://mcp.granola.ai/mcp)
  - linear-server      (https://mcp.linear.app/mcp)

✅ Skills instaladas:
  - superpowers        (13 skills)
  - ui-ux-pro-max      (design system generator)

⚠️ Requieren acción manual:
  - Microsoft 365  → claude.ai Integrations
  - Atlassian      → claude.ai Integrations
  - pencil         → VS Code Extension: Pencil Dev

Reinicia Claude Code para que los MCPs y skills nuevos estén disponibles.
```

## Herramientas CLI adicionales

### RTK — Token Killer

RTK no es un MCP — es un CLI que envuelve comandos de shell para reducir el output de tokens hasta un 90%. Se integra con Claude Code via `rtk init --global`.

**Instalación:**
```bash
# Linux / macOS / WSL
curl -fsSL https://raw.githubusercontent.com/rtk-ai/rtk/refs/heads/master/install.sh | sh

# Cargo
cargo install --git https://github.com/rtk-ai/rtk

# Homebrew
brew install rtk
```

**Verificar instalación:**
```bash
rtk --version
```

**Activar en Claude Code:**
```bash
rtk init --global
```

> Después de `rtk init --global`, todos los comandos Bash deben prefijarse con `rtk` (ej: `rtk git status`, `rtk vitest run`).

---

## Notas de implementación

- En Windows, los MCPs que usan npx requieren `cmd /c "npx -y ..."` para evitar problemas de PATH.
- Si `claude mcp add` falla con "already exists", usa `claude mcp remove <name> -s user` primero.
- MCPs con `-s user` aplican a todos los proyectos. Sin scope aplican solo al proyecto actual.
- GitHub y SQL Server necesitan env vars — nunca hardcodearlas, siempre leer de variables de entorno.
- MCPs URL (granola, linear-server) se conectan via HTTP y pueden requerir auth OAuth en browser.
- MCPs globales (MS 365, Atlassian, pencil) no se instalan via CLI — son integraciones de plataforma.
- Skills se instalan con `claude skills add <repo>` y quedan en `.claude/skills/`.
