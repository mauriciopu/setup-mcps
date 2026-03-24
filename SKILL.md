---
name: setup-mcps
description: Instala y verifica todos los MCP servers y skills usados en wagent-ai (context7, filesystem, github, granola, linear, mermaid, pencil, playwright + skills de superpowers y ui-ux-pro-max)
argument-hint: "--check | --install | --reinstall"
---

# Setup MCPs & Skills — wagent-ai

Instala y verifica los MCP servers y skills requeridos para desarrollo en wagent-ai.

## MCPs requeridos por este proyecto

| MCP | Scope | Propósito | Instalación |
|-----|-------|-----------|-------------|
| `context7` | user | Docs de librerías en tiempo real | `npx -y @context7/mcp-server` |
| `filesystem` | user | Acceso al filesystem local | `npx -y @modelcontextprotocol/server-filesystem` |
| `github` | user | PRs, issues, code search en GitHub | `npx -y @modelcontextprotocol/server-github` + `GITHUB_PERSONAL_ACCESS_TOKEN` |
| `granola` | user | Transcripts de reuniones | URL: `https://mcp.granola.ai/mcp` |
| `linear` | user | Gestión de tareas en Linear | `@emmett.deen/linear-mcp-server` + `LINEAR_API_TOKEN` |
| `mermaid` | user | Generación de diagramas Mermaid | `npx -y @pensive/mermaid-mcp-server` |
| `pencil` | user | Diseño de pantallas UI/UX | Configura via Claude Code Integrations |
| `playwright` | user | Testing E2E y web scraping/documentación | `npx -y @playwright/mcp@latest` |

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

Ejecuta `claude mcp list` y captura la salida. Identifica qué MCPs de la tabla de arriba ya están instalados y cuáles faltan.

Para skills, verifica si existen los directorios en `.claude/skills/`:
- `.claude/skills/test-driven-development/` (de superpowers)
- `.claude/skills/ui-ux-pro-max/` (de ui-ux-pro-max-skill)

Presenta un resumen en tabla:

| Componente | Tipo | Estado | Notas |
|------------|------|--------|-------|
| context7 | MCP | ✅ / ❌ / ⚠️ | ... |
| superpowers | Skill Pack | ✅ / ❌ | ... |
| ... | ... | ... | ... |

### Paso 2 — Instalar MCPs faltantes (solo si `--install` o `--reinstall`)

Para cada MCP faltante, ejecuta el comando correspondiente en orden:

#### context7
```bash
claude mcp add context7 -s user -- npx -y @context7/mcp-server
```

#### filesystem
```bash
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem "D:/Usuarios/mpuyo/Escritorio"
```

#### github
Requiere `GITHUB_PERSONAL_ACCESS_TOKEN`. Verifica si la variable de entorno existe antes de instalar.

```bash
claude mcp add github -s user \
  -e GITHUB_PERSONAL_ACCESS_TOKEN="${GITHUB_PERSONAL_ACCESS_TOKEN}" \
  -- npx -y @modelcontextprotocol/server-github
```

Si la variable no está definida, avisa al usuario y salta este MCP.

#### granola
```bash
claude mcp add granola -s user --url "https://mcp.granola.ai/mcp"
```

> Granola es URL-based — no requiere npx. Puede requerir autenticación en el browser la primera vez.

#### linear
Requiere `LINEAR_API_TOKEN`. Verifica si la variable existe antes de instalar.

```bash
claude mcp add linear -s user \
  -e LINEAR_API_TOKEN="${LINEAR_API_TOKEN}" \
  -- npx -y @emmett.deen/linear-mcp-server
```

Si la variable no está definida, avisa al usuario con este mensaje:
```
⚠️  LINEAR_API_TOKEN no encontrado.
    Obtén tu API key en: https://linear.app/settings/api
    Luego ejecuta: export LINEAR_API_TOKEN="lin_api_..." y vuelve a correr /setup-mcps --install
```

#### mermaid
```bash
claude mcp add mermaid -s user -- npx -y @pensive/mermaid-mcp-server
```

#### playwright
```bash
claude mcp add playwright -s user -- npx -y @playwright/mcp@latest
```

> Playwright MCP permite controlar browsers para testing E2E, scraping y documentación de UIs.
> Si es la primera vez, puede requerir: `npx playwright install` para descargar los browsers.

#### pencil
Pencil no tiene un paquete npm público — se instala desde Claude Code Integrations.
Informa al usuario:
```
ℹ️  pencil se instala desde Claude Code > Settings > Integrations > Pencil.
    No se puede instalar via CLI.
```

### Paso 3 — Instalar Skills faltantes (solo si `--install` o `--reinstall`)

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

### Paso 4 — Verificar instalación

Después de instalar, ejecuta `claude mcp list` de nuevo y verifica que los MCPs aparecen. Para skills, confirma que los directorios existen en `.claude/skills/`.

### Paso 5 — Resumen final

Presenta tabla final con el estado post-instalación:

```
✅ MCPs instalados correctamente:
  - context7
  - filesystem
  - granola
  - linear
  - mermaid
  - playwright

✅ Skills instaladas correctamente:
  - superpowers (13 skills)
  - ui-ux-pro-max (design system generator)

⚠️ Requieren acción manual:
  - github    → necesita GITHUB_PERSONAL_ACCESS_TOKEN
  - pencil    → instalar via Claude Code Integrations

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

Verifica si RTK está instalado con `rtk --version`. Si no está, muestra las opciones de instalación y recomienda `install.sh` en Linux/macOS o Cargo en Windows.

---

## Notas de implementación

- En Windows, algunos MCPs requieren `cmd /c "npx -y ..."` en lugar de `npx -y ...` directamente.
- Si `claude mcp add` falla con "already exists", agrega `--force` o usa `claude mcp remove <name>` primero.
- Los MCPs instalados con `-s user` aplican a todos los proyectos. Los instalados sin scope aplican solo al proyecto actual.
- Linear y GitHub necesitan API keys — nunca hardcodearlas en el comando, siempre leer de env vars.
- Skills se instalan con `claude skills add <repo>` y quedan en `.claude/skills/`.
