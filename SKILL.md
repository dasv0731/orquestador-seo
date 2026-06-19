---
name: orquestador-seo
description: Use as the single entry point for a complete SEO engagement. Conducts the full lifecycle (intake → research → design → plans → build validation → monitor) by routing each phase to the right skill — strategy/structure to seo-master-plan, live data/audits/validation to claude-seo. Triggers on "proyecto SEO completo", "orquestar SEO", "estrategia + auditoría SEO", "plan SEO con datos en vivo", "llevar un cliente SEO de principio a fin".
---

# Orquestador SEO

Punto de entrada único de un proyecto SEO completo. **No hace el trabajo: lo enruta.** Conduce el ciclo de vida delegando cada fase al skill correcto y resolviendo los conflictos entre estrategia y datos reales.

## División de labor (la regla mental)

| Skill | Responde | Aporta |
|---|---|---|
| **`seo-master-plan`** | **QUÉ y POR QUÉ** | Estrategia, estructura, spec §0–§14, planes, principios de honestidad, KPIs como marco |
| **`claude-seo:*`** | **QUÉ ES HOY y SI FUNCIONA** | Datos en vivo, auditorías, keyword volumes, schema gen/validación, técnico, local, GEO, backlinks, drift |

`seo-master-plan` decide la jugada; `claude-seo` mide el campo y valida que funcionó. El orquestador nunca duplica: si una capacidad existe en un skill, se delega.

## Dependencias (verificar antes de empezar)

- **`seo-master-plan`** — instalado en `~/.claude/skills/seo-master-plan/` (repo: `dasv0731/seo-strategy`).
- **`claude-seo`** — cargado como `claude-seo@skills-dir` desde `~/.claude/skills/claude-seo/` (repo: `AgriciDaniel/claude-seo`). Expone skills/agentes namespaced `claude-seo:*`.

Si falta alguno, detente y avisa: el orquestador no puede conducir sin ambos. Comprobar con `claude plugin list` (debe aparecer `claude-seo@skills-dir: loaded`) y `ls ~/.claude/skills/seo-master-plan`.

---

## El ciclo (6 fases)

Conduce en orden, pero **vuelve a fases anteriores** cuando los datos lo exijan (ver Regla de conflicto). Crea un todo por fase.

### Fase 0 · Intake & baseline
**Objetivo:** entender el negocio + fotografiar el estado actual real.
- Estrategia → `seo-master-plan` ref `01-discovery.md` (cuestionario 4 niveles). **No avanzar sin Nivel 1.** Identificar la **restricción dominante** (§0.1).
- Datos (si el sitio existe) → `claude-seo:seo-audit` (auditoría completa), `claude-seo:seo-technical` (crawl/indexabilidad), `claude-seo:seo-google` (GSC/CrUX/GA4 baseline).
- Competidores → `claude-seo:seo-dataforseo` + `claude-seo:seo-backlinks`.
**Salida:** discovery doc + baseline audit. Decisión: ¿greenfield, básico, o migración?

### Fase 1 · Research (data-informed)
**Objetivo:** keywords y clusters anclados en volúmenes reales, no estimaciones.
- Datos → `claude-seo:seo-cluster` (clustering por SERP overlap) + `claude-seo:seo-dataforseo` (volúmenes/dificultad reales) + `claude-seo:seo-sxo` (intent / page-type).
- Estrategia → alimentar `seo-master-plan` ref `08-keywords-clusters.md` (metodología 4 pasos, mapeo 1+3-5, conteo de páginas).
**Salida:** mapa de keywords por cluster con datos en vivo + total de páginas indexables sostenible.

### Fase 2 · Design Spec
**Objetivo:** el spec §0–§14, ahora informado por auditoría + datos reales.
- Estrategia → `seo-master-plan` ref `02-spec-skeleton.md` (conducir con `superpowers:brainstorming`). Arquitectura: ref `03`. Schema: ref `04`. Local: ref `05`. GEO/E-E-A-T: ref `06`. Técnico: ref `07`.
- Validación de diseño → `claude-seo:seo-schema` (validar el grafo JSON-LD propuesto), `claude-seo:seo-geo` (citability/AI), `claude-seo:seo-local`/`claude-seo:seo-maps` (factibilidad local).
**Salida:** spec aprobado en `docs/superpowers/specs/`.

### Fase 3 · Planes de implementación
**Objetivo:** planes ejecutables tarea-por-tarea.
- Estrategia → `seo-master-plan` ref `12-plan-phases.md` (15 fases, Fase 0 pre-requisitos, Self-Review) con `superpowers:writing-plans`. Roadmap/contenido: ref `09`.
- Datos → `claude-seo:seo-content-brief` para briefs competitivos por pieza (complementa `seo-master-plan` `templates/content-brief.md`).
**Salida:** planes en `docs/superpowers/plans/` + briefs de contenido.

### Fase 4 · Build validation
**Objetivo:** validar la implementación contra el spec antes de lanzar.
- Datos → `claude-seo:seo-page` (página por página), `claude-seo:seo-technical`, `claude-seo:seo-schema`, `claude-seo:seo-sitemap`, `claude-seo:seo-hreflang` (si multi-idioma), `claude-seo:seo-images`, `claude-seo:seo-content` (calidad/E-E-A-T/QRG).
- Estrategia → checklist pre-launch de `seo-master-plan` ref `12` (Definition of Done, nunca enlazar a 404).
**Salida:** checklist pre-launch sin bloqueantes.

### Fase 5 · Launch & monitor
**Objetivo:** lanzar y medir contra KPIs + decision gates.
- Datos → `claude-seo:seo-google` (GSC/CrUX/GA4 continuo), `claude-seo:seo-drift` (regresiones post-deploy), `claude-seo:seo-maps` (rank local), `claude-seo:seo-backlinks` (crecimiento de autoridad).
- Estrategia → `seo-master-plan` ref `10-kpis.md` (jerarquía 3 tiers, atribución, decision gates M3/M6/M9) + ref `11` (link building).
**Salida:** dashboard + reporte por cadencia. Volver a Fase 1/2 en cada decision gate.

---

## Regla de conflicto (el corazón del orquestador)

Cuando los **datos de `claude-seo` contradicen un supuesto de `seo-master-plan`**, ganan los datos y se ajusta la estrategia:

- Volumen real insuficiente para un spoke planeado → **no abrir el spoke** (disciplina anti-thin, ref `08`). Documentar y diferir a fase 2 con validación GSC.
- Una página secundaria gana impresiones por la query objetivo → **redirigir el canonical** (ref `03`, anti-canibalización).
- Auditoría revela deuda técnica que mata CWV → **priorizar Fase 4 técnica** antes de escalar contenido.
- Competidor domina un cluster con autoridad inalcanzable → **re-priorizar clusters** hacia gaps reales (ref `11`).

Nunca forzar el plan contra la evidencia. El spec es una hipótesis; los datos la corrigen.

---

## Cómo decidir qué invocar (heurística rápida)

- ¿Es una decisión de **estructura, arquitectura, copy, o principio**? → `seo-master-plan`.
- ¿Necesitas un **número real, un estado actual, o validar algo en vivo**? → `claude-seo:*`.
- ¿Es una **pieza de contenido**? → brief con ambos (`seo-master-plan` template + `claude-seo:seo-content-brief`), redacción con principios de `seo-master-plan` ref `06`/`09`, validación con `claude-seo:seo-content`.
- ¿No estás seguro de qué `claude-seo:*` aplica? → empieza por `claude-seo:seo` (su orquestador interno) o `claude-seo:seo-audit`.

## Anti-patrones

- **Saltarse Fase 0 / la restricción dominante** → plan sin ancla. Igual que en `seo-master-plan`.
- **Construir el spec con volúmenes estimados** cuando `claude-seo:seo-dataforseo` puede dar reales → decisiones sobre arena.
- **Duplicar trabajo** (re-hacer a mano lo que un skill ya hace) → enruta, no reimplementes.
- **Ignorar la Regla de conflicto** → spec bonito que no rankea.
- **Lanzar sin Fase 4** → deuda técnica/schema roto en producción.
