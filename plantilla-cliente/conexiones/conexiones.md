# Conexiones — {CLIENTE}

> **Solo IDs y propiedades.** Los secretos (service account JSON, API keys, login
> DataForSEO) viven a nivel agencia en `~/.config/claude-seo/` y **NUNCA** en este repo.

## Google (vía `claude-seo:seo-google`)
- **GSC property:** `sc-domain:{dominio}`   _(o `https://{dominio}/` si es URL-prefix)_
- **GA4 property:** `properties/{id}`
- **País / locale objetivo:** {EC · es-EC}
- **Service account de agencia:** `{nombre}@{proyecto}.iam.gserviceaccount.com`
  - [ ] Agregado como usuario en **GSC** (Configuración → Usuarios y permisos → Full/Restricted)
  - [ ] Agregado como **Viewer** en **GA4** (Admin → Property Access Management)

## DataForSEO (vía `claude-seo:seo-dataforseo`)
- Login de agencia (compartido, en config global).
- **Location:** {2218 = Ecuador}   ·   **Language:** {es}

## Otros
- **Bing Webmaster:** {property / IndexNow key}
- **GBP:** {url del perfil}

## Cómo se usan (el orquestador pasa estas propiedades como argumento)
```
claude-seo:seo-google gsc   sc-domain:{dominio}
claude-seo:seo-google ga4   properties/{id}
claude-seo:seo-google crux  https://{dominio}/
```

## Verificación de acceso
```bash
# Desde la carpeta del skill claude-seo, comprobar credenciales de agencia:
python3 ~/.claude/skills/claude-seo/skills/seo-google/scripts/google_auth.py --check --json
```
Si el service account aún no está agregado al GSC/GA4 del cliente, las llamadas Tier 1/2 fallarán con permiso denegado → completar los checkboxes de arriba primero.
