# 00 · Alta de cliente (Fase 0.0 · bootstrap)

Procedimiento para dar de alta una cuenta nueva. Precede a la Fase 0 (discovery).

## Pre-requisito (una sola vez por máquina)
- Workspace raíz `~/seo-clientes/` con `REGISTRO.md` existe. Si no, crearlo (ver final).
- Credenciales de agencia configuradas → `references/01-credenciales-agencia.md`.

## Pasos

### 1. Definir el slug
`slug` = nombre corto kebab-case del cliente (ej. `aiotech`, `inspira-bienestar`).

### 2. Copiar la plantilla
```bash
cp -r ~/.claude/skills/orquestador-seo/plantilla-cliente ~/seo-clientes/<slug>
```
(PowerShell: `Copy-Item -Recurse "$env:USERPROFILE\.claude\skills\orquestador-seo\plantilla-cliente" "$env:USERPROFILE\seo-clientes\<slug>"`)

### 3. Rellenar los placeholders
En `~/seo-clientes/<slug>/`:
- `PROJECT.md` — todos los `{...}` (cliente, dominio, vertical, **restricción dominante**, arquitectura tentativa, estado del sitio, alcance, fechas, decision gates).
- `conexiones/conexiones.md` — GSC property, GA4 id, locale, DataForSEO location/language, email del service account.
- `actividades/bitacora.md` — fecha real del alta.

### 4. Verificar acceso a datos del cliente
- Comprobar credenciales de agencia:
  ```bash
  python3 ~/.claude/skills/claude-seo/skills/seo-google/scripts/google_auth.py --check --json
  ```
- Confirmar que el **service account de agencia** está agregado en:
  - GSC del cliente (Configuración → Usuarios y permisos).
  - GA4 del cliente (Admin → Property Access Management → Viewer).
- Marcar los checkboxes en `conexiones/conexiones.md`. Si falta acceso, las llamadas Tier 1/2 fallan → resolver antes de la Fase 5.

### 5. Crear el repo privado + primer commit
```bash
cd ~/seo-clientes/<slug>
git init -b main
git add -A
git commit -m "chore: alta de cliente <slug> — scaffold inicial"
gh repo create <org>/seo-<slug> --private --source=. --remote=origin --push
```
(El `.gitignore` ya mantiene los secretos fuera. `<org>` = tu cuenta/organización de GitHub.)

### 6. Registrar el cliente
Añadir una fila a `~/seo-clientes/REGISTRO.md`.

### 7. Continuar con la Fase 0 (discovery)
Volver al ciclo normal del orquestador con el working dir en `~/seo-clientes/<slug>/`. A partir de aquí, la memoria de Claude Code para este cliente queda aislada por ruta.

---

## Crear el workspace raíz (solo la primera vez)
```bash
mkdir -p ~/seo-clientes
```
Crear `~/seo-clientes/REGISTRO.md` con la tabla:
```markdown
# Registro de clientes SEO

| Cliente | Slug | Dominio | Vertical | Fase | Repo | GSC | GA4 | Inicio |
|---|---|---|---|---|---|---|---|---|
```
