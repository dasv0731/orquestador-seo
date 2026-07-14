# Instalación en una máquina nueva

`orquestador-seo` es el **conductor** del ecosistema SEO por cliente. Depende de las skills dueñas del ecosistema, cada una en su propio repo bajo `~/.claude/skills/`.

## Requisitos
- Claude Code instalado.
- `git` y `gh` (GitHub CLI) autenticado (`gh auth login`).
- `python3` + `pip` (scripts de Google API de `claude-seo` y del pipeline seo-*).

## 1. Clonar el orquestador
```bash
git clone https://github.com/dasv0731/orquestador-seo.git ~/.claude/skills/orquestador-seo
```
(PowerShell: reemplaza `~/.claude` por `"$env:USERPROFILE\.claude"`.)

## 2. Instalar las skills del ecosistema
Cada capacidad la aporta su skill dueña; instálalas en `~/.claude/skills/` desde sus repos privados (`dasv0731`) y sigue el INSTALL de cada una para sus dependencias:

`seo-master-plan` (repo seo-strategy), `arquitectura-seo`, `base-cliente`, `seo-setup-cliente`, `seo-sync`, `extraccion`, `seo-analisis`, `seo-analisis-gsc`, `interlinking`, `schema-graph`, `geo-audit`, `seo-vitals`, `linkbuilding`, `diseno-secciones`, `seo-cambios`, `seo-dashboard`, `content-engine`.

Gap-filler:
```bash
git clone https://github.com/AgriciDaniel/claude-seo.git ~/.claude/skills/claude-seo
cd ~/.claude/skills/claude-seo && ./install.sh   # Windows: .\install.ps1
```
`claude-seo` carga como `claude-seo@skills-dir` (tiene `.claude-plugin/plugin.json`).

## 3. Recargar y verificar
En Claude Code: `/reload-plugins` (o reiniciar). Verifica:
```bash
claude plugin list      # claude-seo@skills-dir: loaded
ls ~/.claude/skills/    # deben verse las skills del ecosistema
```

## 4. Credenciales de agencia (una sola vez)
Configurar `~/.config/claude-seo/google-api.json` (service account con acceso al GSC/GA4 de cada cliente + API key) y el login de DataForSEO. Tokens de DinoRank/Clarity con scope local en `~/.claude.json`. Las gestiona `seo-setup-cliente`. **Nunca en repos.**

## Listo
Da de alta clientes con la **Fase 0.0** del orquestador (crear `Clientes\<slug>\` → `seo-setup-cliente` → `base-cliente`). Cada cliente es su propia carpeta bajo `Clientes\` (sin repo por cliente), con memoria aislada por ruta.

---

### Notas
- Para actualizar `claude-seo`: `cd ~/.claude/skills/claude-seo && git pull` + `/reload-plugins`.
- Las credenciales (`~/.config/claude-seo/`) no están en ningún repo — se configuran a mano por seguridad.
