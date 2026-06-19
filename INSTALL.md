# Instalación en una máquina nueva

El sistema son **3 skills** que deben vivir en un skills directory de Claude Code (`~/.claude/skills/`). `orquestador-seo` depende de los otros dos.

## Requisitos
- Claude Code instalado.
- `git` y `gh` (GitHub CLI) autenticado (`gh auth login`).
- `python3` + `pip` (para los scripts de Google API de `claude-seo`).

## 1. Clonar los 3 skills

**macOS / Linux / Git Bash:**
```bash
git clone https://github.com/dasv0731/seo-strategy.git    ~/.claude/skills/seo-master-plan
git clone https://github.com/dasv0731/orquestador-seo.git ~/.claude/skills/orquestador-seo
git clone https://github.com/AgriciDaniel/claude-seo.git  ~/.claude/skills/claude-seo
```

**Windows / PowerShell:**
```powershell
$skills = "$env:USERPROFILE\.claude\skills"
git clone https://github.com/dasv0731/seo-strategy.git    "$skills\seo-master-plan"
git clone https://github.com/dasv0731/orquestador-seo.git "$skills\orquestador-seo"
git clone https://github.com/AgriciDaniel/claude-seo.git  "$skills\claude-seo"
```

> `seo-master-plan` y `orquestador-seo` cargan como skills personales (tienen `SKILL.md` en su raíz).
> `claude-seo` carga como `claude-seo@skills-dir` automáticamente (tiene `.claude-plugin/plugin.json`).

## 2. Dependencias de claude-seo
```bash
cd ~/.claude/skills/claude-seo && ./install.sh      # Windows: .\install.ps1
# (o:  pip install -r requirements.txt)
```

## 3. Recargar y verificar
En Claude Code: `/reload-plugins` (o reiniciar). Verifica:
```bash
claude plugin list      # debe verse: claude-seo@skills-dir: loaded
```
Y que los skills `seo-master-plan` y `orquestador-seo` aparezcan disponibles.

## 4. Workspace de agencia
```bash
mkdir -p ~/seo-clientes
```
Crear `~/seo-clientes/REGISTRO.md` (plantilla al final de `references/00-alta-cliente.md`).

## 5. Credenciales de agencia (una sola vez)
Configurar `~/.config/claude-seo/google-api.json` (service account + API key) y DataForSEO.
Guía completa: `references/01-credenciales-agencia.md`.

## Listo
Da de alta clientes con la **Fase 0.0** (`references/00-alta-cliente.md`). Cada cliente es su propia carpeta + repo privado, con memoria aislada.

---

### Notas
- Los **repos de cliente son privados y aparte** — no se clonan aquí; se crean por cliente con la Fase 0.0.
- Para actualizar `claude-seo` a la última versión del autor: `cd ~/.claude/skills/claude-seo && git pull` + `/reload-plugins`.
- Las credenciales (`~/.config/claude-seo/`) **no están en ningún repo** — se configuran a mano en cada máquina por seguridad.
