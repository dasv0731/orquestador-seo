# {CLIENTE} — Proyecto SEO

Proyecto gestionado con el skill [`orquestador-seo`](https://github.com/dasv0731/orquestador-seo). Working dir de este cliente → su memoria de Claude Code queda aislada automáticamente.

## Estructura
```
PROJECT.md            Ficha durable: dominio, vertical, restricción dominante, fase, enlaces
conexiones/           GSC property, GA4 id, locale, DataForSEO target (SIN secretos)
actividades/          bitacora.md — log cronológico de lo hecho
discovery/            Cuestionario de intake (seo-master-plan ref 01)
specs/                Design spec §0–§14 (seo-master-plan ref 02)
plans/                Planes de implementación por mes/trimestre (seo-master-plan ref 12)
keywords/             Estudios de KW con datos reales (claude-seo)
reportes/             Reportes por cadencia (KPIs, decision gates)
.gitignore            Mantiene los secretos fuera del repo
```

## Fase actual
Ver `PROJECT.md`. El ciclo lo conduce `orquestador-seo` (intake → research → spec → planes → build → monitor).
