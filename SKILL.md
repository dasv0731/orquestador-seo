---
name: orquestador-seo
description: Use as the single entry point for a complete SEO engagement. Conducts the full lifecycle (bootstrap → diagnosis/framing → architecture → spec/plans → build/validation → monitor) by routing each phase to the OWNING skill of the ecosystem (base-cliente, seo-setup-cliente, seo-sync, extraccion, arquitectura-seo, seo-master-plan, diseno-secciones, schema-graph, interlinking, geo-audit, seo-vitals, linkbuilding, seo-analisis, seo-analisis-gsc, seo-cambios, seo-dashboard, content-engine); claude-seo:* only fills gaps. Triggers on "proyecto SEO completo", "orquestar SEO", "estrategia + auditoría SEO", "plan SEO con datos en vivo", "llevar un cliente SEO de principio a fin".
---

# Orquestador SEO

Punto de entrada único de un proyecto SEO completo. **No hace el trabajo: lo enruta** a la skill dueña de cada capacidad y resuelve los conflictos entre la estrategia y los datos reales.

## Frontera (invariante)

- El orquestador es **skill** y es **runtime**: secuencia skills, gestiona estado/gates y aplica la arista de retorno. No toma decisiones de SEO.
- `seo-master-plan` = **artefacto** (spec §0–§14 + planes). El orquestador lo invoca; no lo suplanta.
- **Sustrato de comunicación:** las skills no se llaman entre sí; dejan/leen archivos en la carpeta del cliente `Clientes\<slug>\` y tablas en `data\seo.db`. El mapa producto/consumo vive en `references/02-contratos.md`.

## División de labor (la regla mental)

Cada capacidad tiene UNA skill dueña. `claude-seo:*` es **gap-filler**: solo para lo que el ecosistema no cubre.

| Dominio | Skill dueña |
|---|---|
| Estrategia, spec §0–§14, planes, KPIs/gates, principios, migración | **seo-master-plan** (CONSUME artefactos) |
| Árbol de URLs + estudio de keywords | **arquitectura-seo** (`arquitectura.csv`, `enlazado.csv`, `mapeo-301.csv`, `mapa-keywords.csv`, `sitio.yaml`) |
| Conocimiento de negocio | **base-cliente** (`base\contexto-<slug>.md`) |
| Conexiones + poblar/actualizar seo.db | **seo-setup-cliente** (alta) · **seo-sync** (ingesta) |
| Crawl del sitio (HTML, schemas, issues, inlinks) | **extraccion** |
| Diagnóstico de datos (huérfanas, index bloat, CTR, quick wins, canibalización, 404) | **seo-analisis** · **seo-analisis-gsc** |
| PageRank interno / enlazado real | **interlinking** |
| JSON-LD conectado del dominio | **schema-graph** |
| Visibilidad/citability AI | **geo-audit** |
| Core Web Vitals | **seo-vitals** |
| Backlinks / off-page | **linkbuilding** |
| Secciones/layout/copy de UNA página | **diseno-secciones** |
| Análisis de HTML semántico de una página | **html-semantico** |
| Redacción informacional (silo aparte) | **content-engine** |
| Veredicto antes/después de un cambio | **seo-cambios** |
| Reporte / dashboard | **seo-dashboard** |
| **Gap-filler** (content-brief competitivo, auditoría single-page ad-hoc, hreflang/i18n, fallback si falta una skill propia) | **claude-seo:*** |

El orquestador nunca duplica: si una capacidad existe en una skill, se delega.

## Dependencias (verificar antes de empezar)

Skills del ecosistema esperadas en `~/.claude/skills/` (repos privados en `dasv0731`): `seo-master-plan`, `arquitectura-seo`, `base-cliente`, `seo-setup-cliente`, `seo-sync`, `extraccion`, `seo-analisis`, `seo-analisis-gsc`, `interlinking`, `schema-graph`, `geo-audit`, `seo-vitals`, `linkbuilding`, `diseno-secciones`, `html-semantico`, `seo-cambios`, `seo-dashboard`, `content-engine`. Gap-filler: `claude-seo` (se auto-carga como `claude-seo@skills-dir`).

Si falta una skill crítica para la fase en curso, detente y avisa. Comprueba con `claude plugin list` y `ls ~/.claude/skills/`.

## Multi-cliente (agencia)

Cada cuenta es un **proyecto aislado** con su carpeta canónica `C:\Users\Marke\Documents\Respaldo SEO\Clientes\<slug>\`. El working dir al trabajar un cliente es esa carpeta → la **memoria nativa de Claude Code queda aislada por cliente** (se indexa por ruta de proyecto). **No hay repo git por cliente ni registro central**: la carpeta + `data\seo.db` son la fuente de verdad.

Credenciales = **agencia, una sola vez**: service account de Google en `~/.config/claude-seo/google-api.json` (con acceso al GSC/GA4 de cada cliente), login DataForSEO, tokens DinoRank/Clarity con scope local en `~/.claude.json`. Las gestiona **seo-setup-cliente**; el orquestador no las documenta de nuevo. Nunca hay secretos en `Clientes\`.

### Fase 0.0 · Bootstrap de cliente (delegado)
Da de alta una cuenta nueva secuenciando skills dueñas — **no reimplementa alta**:
1. Crear `Clientes\<slug>\` (slug kebab-case, minúsculas, sin tildes/espacios).
2. **seo-setup-cliente** → `conexiones\conexiones.json` + `data\seo.db`.
3. **base-cliente** → `base\` + `base\contexto-<slug>.md`.

**Gate de arranque:** no entrar al ciclo sin `conexiones\conexiones.json`, `data\seo.db` y `base\contexto-<slug>.md`.

## El ciclo (por escenario)

**Fase 0 · Determinar el escenario.** ¿Hay sitio vivo con GSC/GA4 pobladas y contenido que crawlear? → **A (existente)**. ¿No hay sitio ni datos? → **B (greenfield)**.

**Espina común** (el master se parte en FRAMING y ENSAMBLAJE alrededor de arquitectura-seo):
`base-cliente → master FRAMING (§0–§2 → arquitectura\data\enfoque.md) → arquitectura-seo (árbol) → master ENSAMBLAJE (§3–§14 → spec+planes)`.

Crea un todo por paso y vuelve a pasos anteriores cuando los datos lo exijan (Regla de conflicto).

### Escenario A — existente (diagnosticar → rediseñar → migrar → monitorear)
```
0. seo-setup-cliente + base-cliente                          (Fase 0.0)
1. seo-sync BACKFILL  ∥  extraccion crawl                    (paralelo)
2. DIAGNÓSTICO (leen lo de 1):
     seo-analisis · seo-analisis-gsc · interlinking ·
     schema-graph · geo-audit · seo-vitals · linkbuilding
3. seo-master-plan FRAMING (§0–§2 → enfoque.md; restricción dominante)
4. arquitectura-seo (modo migración → arquitectura.csv + mapeo-301.csv)
5. seo-master-plan ENSAMBLAJE (§3–§14 → spec sabor MIGRACIÓN + planes)
6. Ejecución: diseno-secciones · content-engine · schema-graph
7. MONITOREO: seo-cambios (logchange + veredicto) · seo-dashboard ·
     re-corridas geo-audit/seo-vitals/linkbuilding · seo-sync diario
```

### Escenario B — greenfield (diseñar → construir → validar → lanzar → monitorear)
```
0. seo-setup-cliente + base-cliente                          (Fase 0.0)
1. seo-master-plan FRAMING (§0–§2 → enfoque.md)
2. arquitectura-seo (modo greenfield: estudio de kws → árbol nuevo, sin inventario/301)
3. seo-master-plan ENSAMBLAJE (§3–§14 → spec sabor FOUNDATION + planes)
4. BUILD: diseno-secciones (modo propuesta) · content-engine · schema-graph
5. VALIDACIÓN pre-launch (staging): seo-vitals · geo-audit · interlinking · html-semantico
6. LAUNCH: sitemap + IndexNow + solicitar indexación
7. MONITOREO: seo-sync empieza a capturar GSC/GA4 → converge con Escenario A
```
A y B convergen en el **monitoreo con seo.db poblado**.

## Gate de medición (antes de abrir cualquier loop de ejecución)

La medición es **pre-requisito, no fase final**. Antes de tocar contenido/técnico/enlazado (ejecución) debe existir: **baseline** registrado (crawl + `seo.db` con GSC/GA4 vía seo-sync) y **`changes_log` activo** para marcar el antes/después. Sin esto, los loops corren a ciegas. Lo instala **seo-setup-cliente** (crea `changes_log`) + **seo-sync** (puebla el baseline); cada cambio se registra con **seo-cambios** (`logchange`).

## Regla de conflicto + arista de retorno (el corazón del orquestador)

Cuando los **datos contradicen un supuesto del spec**, ganan los datos — pero **no todo dato es señal**. Clasifica el veredicto (lo emite **seo-cambios**: `changes_log` → `report.py --report beforeafter`) en 3 ramas:

1. **Alta confianza (`mejora` / `regresion`)** → propaga. Si confirma el supuesto, refuerza el criterio; si lo **invalida**, reabre el gate de diseño (FRAMING/arquitectura-seo).
2. **`no_determinable`** (muestra o madurez insuficiente) → **NO propaga.** El ruido no reescribe criterios ("aprender de fantasmas"). Espera más datos.
3. **`sin_efecto`** (datos suficientes, sin movimiento) → nulo confiable; registra que no movió la aguja, no reabras diseño.

Ejemplos de conflicto que (con veredicto de alta confianza) ajustan la estrategia:
- Volumen real insuficiente para un spoke planeado → **no abrir el spoke** (anti-thin; arquitectura-seo / master §9).
- Una página secundaria gana impresiones por la query objetivo → **redirigir el canonical** (anti-canibalización; seo-analisis-gsc + arquitectura-seo).
- Auditoría revela deuda técnica que mata CWV (seo-vitals) → **priorizar lo técnico** antes de escalar contenido.
- Competidor domina un cluster con autoridad inalcanzable → **re-priorizar clusters** hacia gaps reales (linkbuilding).

Nunca forzar el plan contra la evidencia, **pero tampoco dejar que el ruido lo reescriba**.

## Estado compartido y costuras transversales

El estado compartido (`Clientes\<slug>\` + `seo.db`) es la **fuente única de verdad**: las skills de diseño escriben, las de ejecución leen. El mapa producto/consumo por skill, con su estado de frontera (🟢 firme / 🟡 en prueba / 🔴 de riesgo) y el experimento vivo 3↔5, está en `references/02-contratos.md`. Consúltalo ante cualquier duda de "a quién le toca".

Algunas decisiones **no pertenecen a una sola skill** (costuras que varias heredan):
- **Modo de render (CSR/SSR/estático)** — se decide una vez en discovery (master FRAMING) y lo heredan a la vez la **indexabilidad** (crawl de extraccion + arquitectura-seo) y el **rendimiento** (seo-vitals). Crítico en sitios programáticos: si las páginas a escala son CSR, la % de indexación se desploma; verificar el render **antes** de escalar contenido.
- **i18n** — módulo condicional; único caso gap-filler estructural → `claude-seo:seo-hreflang`. Solo si el negocio es multi-región.

## Cómo decidir qué invocar (heurística rápida)

- ¿Estructura, arquitectura, árbol de URLs o estudio de kws? → **arquitectura-seo** (y el master lo consume).
- ¿Estrategia, spec, planes, principios o KPIs? → **seo-master-plan**.
- ¿Un número real, un estado actual o validar en vivo? → la skill dueña del dominio (seo-vitals CWV, geo-audit AI, linkbuilding backlinks, interlinking enlazado, seo-analisis/gsc datos).
- ¿Secciones/layout/copy de UNA página? → **diseno-secciones**.
- ¿Una pieza de contenido informacional? → **content-engine** (brief competitivo con `claude-seo:seo-content-brief` si aplica).
- ¿Algo sin dueño en el ecosistema (hreflang, auditoría single-page ad-hoc)? → **claude-seo:*** (gap-filler).

## Anti-patrones

- **Saltarse Fase 0 / la restricción dominante** → plan sin ancla.
- **Construir el spec con volúmenes estimados** cuando arquitectura-seo da reales → decisiones sobre arena.
- **Duplicar trabajo** (rehacer a mano lo que una skill ya hace) → enruta, no reimplementes.
- **Usar claude-seo como fuente primaria** cuando existe la skill dueña → rompe el modelo gap-filler.
- **Ignorar la Regla de conflicto** o dejar que el ruido reescriba el plan → spec bonito que no rankea, o aprender de fantasmas.
- **Ejecutar sin el Gate de medición** (baseline + changes_log) → cambios sin veredicto defendible.
