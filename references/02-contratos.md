# 02 · Contratos de estado (mapa vivo, NO contrato congelado)

Este documento hace **legible** el estado compartido que las skills ya producen y consumen de facto. **No fija fronteras**: las describe con un *estado* explícito. La frontera correcta solo se valida ejecutando — aquí solo se anota qué clave de estado cruza cada handoff y cuánta confianza hay en ese corte.

> **Cómo leerlo.** Cada fila mapea una responsabilidad a (a) la clave/artefacto que *recibe*, (b) lo que *entrega*, (c) el *handoff* como sustantivo inspeccionable, (d) el predicado *Hecho* único, y (e) el **estado de frontera**. El estado es lo que evita que esto osifique:
>
> - 🟢 **firme** — invariante o ya validada por ejecución; el corte no está en duda.
> - 🟡 **en prueba** — frontera bajo experimento activo; **no** tratar como cerrada.
> - 🔴 **de riesgo** — vigilar; primera candidata a re-cortar si la ejecución lo pide.

El estado compartido es la fuente única de verdad. Las skills de diseño lo escriben; las de ejecución lo leen. El orquestador solo gestiona estado, gates de dependencia y la arista de retorno (`SKILL.md` → *Regla de conflicto*). No toma decisiones de SEO.

---

## Capa 1 — Diseño (gate secuencial)

| # | Responsabilidad | Dónde vive hoy | Recibe | Entrega (clave) | Handoff (sustantivo) | Hecho (predicado único) | Frontera |
|---|---|---|---|---|---|---|---|
| 1 | Mapeo negocio + intención | `master-plan` §0, §0.1, §1 · `01-discovery` | contexto de negocio (externo) | `mapa_paginas_ingreso` + 4 params de nicho | mapa de páginas con intención y rol económico | ninguna página existe "porque sí"; params de nicho fijados | 🟢 firme (sin vecina aguas arriba) |
| 2 | Dimensionamiento de demanda | `master-plan` §9 · `claude-seo:seo-dataforseo`/`seo-cluster` | `mapa_paginas_ingreso` | `mapa_demanda` | clusters dimensionados + veredicto justifica/no por rama | cada rama tiene demanda medida y veredicto | 🟢 firme |
| 3 | Taxonomía / tipos de página | `master-plan` §2–§3 · `03-architecture` | `mapa_demanda`, `mapa_paginas_ingreso` | `arbol_taxonomia` | árbol de tipos de página | cada nodo agrupa semántica coherente; pasa escalabilidad 10× | 🟡 **en prueba (3↔5)** · ver abajo |
| 4 | Convención de URLs | `master-plan` §3 (**ya plegada en 3**) | `arbol_taxonomia` | `spec_urls` | spec de URLs | toda clase de URL tiene patrón; reversibilidad ok | 🟢 firme (fusión ya hecha) |
| 5 | Enlazado interno | `master-plan` §4 · `claude-seo:seo-cluster` | `arbol_taxonomia`, `spec_urls`, `mapa_paginas_ingreso` | `modelo_enlazado` | reglas de enlace por tipo + flujo a money pages | money pages reciben flujo; sin nodos huérfanos | 🟡 **en prueba (3↔5)** · ver abajo |

## Capa 3 — Medición como gate (antes de ejecutar)

| # | Responsabilidad | Dónde vive hoy | Recibe | Entrega | Handoff | Hecho | Frontera |
|---|---|---|---|---|---|---|---|
| 10 | Medición e instrumentación | `master-plan` §12 · `claude-seo:seo-google` · **pipeline `_tooling`** (`seo.db`, `changes_log`, `report.py`, dashboard) | `contrato_arquitectura` congelado | `sistema_medicion` + protocolo de inferencia | sistema de medición operativo + baseline | todo cambio 6→9 rastreable a un veredicto (`beforeafter`) | 🟢 firme (gate, no fase final — ver `SKILL.md`) |

## Capa 2 — Ejecución (loops concurrentes)

| # | Responsabilidad | Dónde vive hoy | Recibe | Entrega | Handoff | Hecho | Frontera |
|---|---|---|---|---|---|---|---|
| 6 | Rastreo / indexabilidad | `master-plan` §8 · `claude-seo:seo-technical`/`seo-sitemap` · `sync_index.py` | `contrato_arquitectura` + **modo de renderizado** | `estado_rastreo` | sitio rastreable e indexable conforme al árbol | el buscador indexa exactamente lo designado; brecha cerrada o explicada | 🔴 de riesgo (renderizado enreda 6↔8) |
| 7 | Contenido + on-page (+schema) | `master-plan` §5, §10, §14 · `claude-seo:seo-content`/`seo-schema`/`seo-page` | `arbol_taxonomia`, `mapa_demanda`, `modelo_enlazado` | `cobertura_contenido` | páginas optimizadas + schema por tipo | cada tipo cumple plantilla on-page y schema | 🟢 firme (schema covaría con on-page, no con 6/8) |
| 8 | Rendimiento | `master-plan` §8 · `claude-seo:seo-performance`/`seo-google` (CrUX) | plataforma/plantillas + **modo de renderizado** | `estado_rendimiento` | plantillas dentro de umbral CWV | CWV verde por plantilla, o trade-off documentado | 🔴 de riesgo (renderizado enreda 6↔8) |
| 9 | Off-page / autoridad | `master-plan` §11 · `claude-seo:seo-backlinks` | `mapa_paginas_ingreso`, `contrato_arquitectura` | `estado_autoridad` | flujo continuo de señales de autoridad | **no cierra** — se mide por ritmo y calidad | 🟢 firme (track separado continuo) |

## Capa 3 — Gobierno continuo

| # | Responsabilidad | Dónde vive hoy | Recibe | Entrega | Handoff | Hecho | Frontera |
|---|---|---|---|---|---|---|---|
| 11 | Revisión de obsolescencia | `master-plan` ppio. 9 (mínimo) + decision gates M3/M6/M9 · `claude-seo:seo-drift` (regresión) | criterios de todas + veredictos de 10 | `lista_revision` | dictamen de vigencia de criterios | **no cierra** — corre periódico | 🟡 mecanismo añadido, **sin correr aún** (faltan datos post-ejecución) |

---

## Costuras transversales (no son skills; varias las heredan)

- **Modo de renderizado (CSR/SSR/estático)** → decisión de arquitectura que **6 y 8 heredan a la vez**. No pertenece a ninguna caja. Se decide en discovery y se declara en el spec (`master-plan` ppio. 9 + §8). Por eso 6 y 8 están marcadas 🔴: su frontera la enreda esta costura, no un acoplamiento entre ellas.
- **i18n** → módulo condicional. Solo se instancia si el negocio es multi-región (`claude-seo:seo-hreflang`). No es paso fijo.

---

## La frontera 🟡 en prueba: taxonomía (3) ↔ enlazado (5)

Es la frontera de **mayor riesgo** del sistema y **no se decide en escritorio**. Hoy §3 y §4 son secciones separadas del spec, pero comparten lectura del mismo artefacto (`arbol_taxonomia`). La pregunta abierta: ¿son dos skills o una?

**Estado de estado compartido (lo que este documento hace explícito, sin cerrarlo):**
- 5 *depende del artefacto* de 3 (`arbol_taxonomia`) — dependencia sana.
- Lo que falta saber: si 5 depende también de las *tripas* de 3 (de cómo se llegó al árbol) → eso sería acoplamiento y obligaría a fusionar.

**Cómo se resuelve (instrumentado, no opinado):** el experimento corre en **metalectro** (6 clusters auditados, baselines congelados). Cada cambio se registra con `logchange.py --area taxonomia|enlazado|ambos`; el veredicto sale de `report.py --report covariacion`:
- ≥60% de cambios tocó `ambos` → **fusionar 3 y 5** (mover esta fila a 🟢 con §3+§4 unificadas).
- ≤30% → **mantener separadas** (mover a 🟢 con el corte actual confirmado).
- zona gris → seguir 🟡, registrar más.

Cuando el veredicto llegue con alta confianza, **se actualiza este documento y el spec** — esa es la única forma legítima de cerrar la fila. Ver `metalectro/ROADMAP.md` → *Experimento de frontera 3↔5*.

---

## Qué NO es este documento

- **No** es la re-arquitectura a contrato tipado de las 11 skills (eso se difiere hasta que la ejecución valide las fronteras).
- **No** congela ningún corte: las filas 🟡/🔴 son explícitamente provisionales.
- **No** reemplaza al spec ni a los SKILL.md: los mapea, para que el orquestador valide handoffs y para que el experimento 3↔5 sea interpretable.
