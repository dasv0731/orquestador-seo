# orquestador-seo

Skill **conductor** de Claude Code: el punto de entrada único de un proyecto SEO completo. No hace el trabajo — lo **enruta** a la skill dueña de cada capacidad del ecosistema SEO por cliente.

## Qué hace

Conduce el ciclo de vida completo delegando cada fase a su skill dueña y resolviendo los conflictos entre la estrategia y los datos reales. Es **runtime**; el artefacto (spec+planes) lo produce `seo-master-plan`.

Cada capacidad tiene una dueña — estrategia (`seo-master-plan`), árbol de URLs + keywords (`arquitectura-seo`), negocio (`base-cliente`), datos (`seo-setup-cliente`/`seo-sync`/`seo-analisis`/`seo-analisis-gsc`), crawl (`extraccion`), enlazado (`interlinking`), schema (`schema-graph`), AI (`geo-audit`), CWV (`seo-vitals`), backlinks (`linkbuilding`), secciones de página (`diseno-secciones`), contenido (`content-engine`), veredicto de cambios (`seo-cambios`), reporte (`seo-dashboard`). `claude-seo:*` es **gap-filler** (content-brief, single-page ad-hoc, hreflang).

### El ciclo (por escenario)

Fase 0 determina el escenario:
```
A · existente   diagnóstico → arquitectura (migración) → spec → ejecución → monitoreo
B · greenfield  arquitectura (nueva) → spec → build → validación → launch → monitoreo
```
Espina común: `base-cliente → master FRAMING (enfoque.md) → arquitectura-seo → master ENSAMBLAJE (spec+planes)`.

El núcleo es la **regla de conflicto**: cuando los datos (veredicto de `seo-cambios`) contradicen un supuesto del spec con alta confianza, ganan los datos y se reabre el diseño; el ruido (`no_determinable`) se ignora.

## Raíz por cliente

Cada cliente es un proyecto aislado en `C:\Users\Marke\Documents\Respaldo SEO\Clientes\<slug>\` (sin repo git por cliente). La memoria nativa de Claude Code queda aislada por ruta. Credenciales = agencia (`~/.config/claude-seo/google-api.json` + DataForSEO/DinoRank), gestionadas por `seo-setup-cliente`.

Alta de cliente (Fase 0.0): crear la carpeta → `seo-setup-cliente` → `base-cliente`. El orquestador delega; no reimplementa alta. Detalle en `SKILL.md`.

## Dependencias

Skills del ecosistema en `~/.claude/skills/` (repos privados `dasv0731`): seo-master-plan, arquitectura-seo, base-cliente, seo-setup-cliente, seo-sync, extraccion, seo-analisis, seo-analisis-gsc, interlinking, schema-graph, geo-audit, seo-vitals, linkbuilding, diseno-secciones, seo-cambios, seo-dashboard, content-engine. Gap-filler: `claude-seo` (`claude-seo@skills-dir`).

## Uso

Se activa ante *"proyecto SEO completo"*, *"orquestar SEO"*, *"estrategia + auditoría SEO"*, *"llevar un cliente SEO de principio a fin"*.
