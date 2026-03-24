---
name: setup-mcps
description: Instala y verifica todos los MCP servers y skills usados en wagent-ai — 12 MCPs (7 CLI + 2 URL + 3 globales) + 19 skills (2 packs + 17 individuales) + RTK CLI
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

### Skill Packs (repos multi-skill)

| Skill Pack | Repo | Propósito |
|------------|------|-----------|
| `superpowers` | `obra/superpowers` | TDD, debugging sistemático, plan execution, worktrees, code review, subagentes |
| `ui-ux-pro-max` | `nextlevelbuilder/ui-ux-pro-max-skill` | Design system generator, 67 estilos UI, 161 industry rules (incluye Hotel), shadcn/ui + Tailwind |

### Skills individuales

| Skill | Repo | Propósito |
|-------|------|-----------|
| `typescript-best-practices` | `0xbigboss/claude-code` | Type-first development, discriminated unions, exhaustive handling |
| `vercel-react-best-practices` | `vercel-labs/agent-skills` | React/Next.js performance patterns de Vercel Engineering |
| `next-best-practices` | `vercel-labs/next-skills` | File conventions, RSC, data patterns, metadata, error handling |
| `nodejs-best-practices` | `davila7/claude-code-templates` | Async patterns, seguridad, arquitectura Node.js |
| `api-security-best-practices` | `sickn33/antigravity-awesome-skills` | Auth, input validation, rate limiting, OWASP |
| `code-review-excellence` | `wshobson/agents` | Code review constructivo, catch bugs, knowledge sharing |
| `code-review-security` | `hieutrtr/ai1-skills` | Security review: SQLi, XSS, CSRF, secrets detection |
| `docker-expert` | `sickn33/antigravity-awesome-skills` | Multi-stage builds, security hardening, orchestration |
| `redis-development` | `redis/agent-skills` | Redis data structures, vector search, semantic caching |
| `supabase-postgres-best-practices` | `supabase/agent-skills` | PostgreSQL optimization, schema design, queries |
| `vitest` | `antfu/skills` | Unit testing con Vite, mocking, coverage, fixtures |
| `logging-best-practices` | `boristane/agent-skills` | Wide events, canonical log lines, debugging |
| `zod-schema-validation` | `mindrally/skills` | Zod schemas, runtime validation, type inference |
| `shadcn` | `shadcn/ui` | Componentes UI, registries, presets, styling |
| `devops-engineer` | `jeffallan/claude-skills` | CI/CD, Kubernetes, Terraform, GitHub Actions |
| `pnpm` | `antfu/skills` | Workspaces, catalogs, patches, strict resolution |
| `mcp-builder` | `anthropics/skills` | Crear MCP servers con FastMCP o MCP SDK |

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
- `.claude/skills/typescript-best-practices/` (skill individual)

Presenta un resumen en tabla:

| Componente | Tipo | Estado | Notas |
|------------|------|--------|-------|
| context7 | MCP CLI | ✅ / ❌ / ⚠️ | ... |
| granola | MCP URL | ✅ / ❌ | ... |
| Microsoft 365 | MCP Global | ✅ / ❌ | ... |
| superpowers | Skill Pack | ✅ / ❌ | ... |
| typescript-best-practices | Skill | ✅ / ❌ | ... |
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

#### Skill Packs

```bash
# superpowers — 13 skills: TDD, debugging, plans, worktrees, code review, subagentes
claude skills add obra/superpowers

# ui-ux-pro-max — Design system generator, 67 estilos, 161 industry rules
claude skills add nextlevelbuilder/ui-ux-pro-max-skill
```

> ui-ux-pro-max requiere Python 3 para el script de búsqueda de design systems.

#### Skills individuales

Ejecuta cada una en secuencia. Si ya existe, `claude skills add` la salta automáticamente.

```bash
# TypeScript & Frontend
claude skills add 0xbigboss/claude-code                  # typescript-best-practices
claude skills add vercel-labs/agent-skills                # vercel-react-best-practices
claude skills add vercel-labs/next-skills                 # next-best-practices
claude skills add shadcn/ui                               # shadcn

# Backend & Runtime
claude skills add davila7/claude-code-templates           # nodejs-best-practices
claude skills add boristane/agent-skills                  # logging-best-practices
claude skills add redis/agent-skills                      # redis-development
claude skills add supabase/agent-skills                   # supabase-postgres-best-practices

# Validation & Testing
claude skills add mindrally/skills                        # zod-schema-validation
claude skills add antfu/skills                            # vitest + pnpm

# Security & Code Review
claude skills add sickn33/antigravity-awesome-skills      # api-security + docker-expert
claude skills add wshobson/agents                         # code-review-excellence
claude skills add hieutrtr/ai1-skills                     # code-review-security

# DevOps & Infra
claude skills add jeffallan/claude-skills                 # devops-engineer
claude skills add anthropics/skills                       # mcp-builder
```

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

✅ Skill Packs instalados:
  - superpowers        (13 skills)
  - ui-ux-pro-max      (design system generator)

✅ Skills individuales instaladas (17):
  - typescript-best-practices, vercel-react-best-practices, next-best-practices
  - nodejs-best-practices, logging-best-practices, redis-development
  - supabase-postgres-best-practices, zod-schema-validation, vitest, pnpm
  - api-security-best-practices, code-review-excellence, code-review-security
  - docker-expert, devops-engineer, shadcn, mcp-builder

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
