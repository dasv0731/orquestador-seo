# 01 · Credenciales de agencia (setup único compartido)

Modelo elegido: **un set de credenciales para todas las cuentas**. Se configura una vez; cada cliente solo aporta *qué propiedad* usar (en su `conexiones/conexiones.md`).

## 1. Google (GSC · GA4 · CrUX · PageSpeed) — vía `claude-seo:seo-google`

`claude-seo` lee un config global:
```
~/.config/claude-seo/google-api.json
```
```json
{
  "service_account_path": "/ruta/segura/service_account.json",
  "api_key": "<GOOGLE_API_KEY>",
  "default_property": "sc-domain:ejemplo.com",
  "ga4_property_id": "properties/000000000"
}
```

Setup guiado: ejecuta `claude-seo:seo-google` → comando `setup`, o lee
`~/.claude/skills/claude-seo/skills/seo-google/references/auth-setup.md`.

Pasos (resumen):
1. Proyecto en Google Cloud + habilitar APIs: Search Console API, Analytics Data API, PageSpeed Insights API, (opcional) Indexing API.
2. Crear **un service account** + descargar su JSON → guardarlo **fuera de cualquier repo** (ej. `~/.config/claude-seo/`).
3. Crear una **API key** (para PSI/CrUX, Tier 0).
4. Rellenar `google-api.json`.

> `default_property` / `ga4_property_id` son solo un fallback. En multi-cliente
> **se pasa la propiedad por argumento** desde el `conexiones.md` del cliente,
> así que el default puede apuntar a cualquiera (o dejarse vacío).

### Dar acceso por cada cliente (el paso que se repite)
El service account es **un usuario** al que hay que invitar en cada cuenta:
- **GSC del cliente** → Configuración → Usuarios y permisos → agregar el email del service account (`...@...iam.gserviceaccount.com`).
- **GA4 del cliente** → Admin → Property Access Management → agregar como **Viewer**.

Sin esto, las llamadas Tier 1 (GSC) y Tier 2 (GA4) de ese cliente fallan.

## 2. DataForSEO — vía `claude-seo:seo-dataforseo`
Un solo login de agencia (es data de SERP/keywords, no auth por cliente). Se configura como extensión MCP de `claude-seo`. Por cliente solo varía `location` + `language` (en su `conexiones.md`).

## 3. Tiers de capacidad (recordatorio)
| Tier | Requiere | Habilita |
|---|---|---|
| 0 | API key | pagespeed, crux, crux-history, nlp, youtube |
| 1 | + service account (con acceso al GSC del cliente) | gsc, inspect, sitemaps, index |
| 2 | + ga4_property_id (acceso GA4 del cliente) | ga4, ga4-pages |
| 3 | + Google Ads token | keywords, volume (Keyword Planner) |

## Seguridad
- El JSON del service account y las API keys **viven fuera de los repos de cliente** (en `~/.config/claude-seo/`). El `.gitignore` de la plantilla los bloquea por si acaso.
- Rotar la API key / regenerar el service account si se filtran.
