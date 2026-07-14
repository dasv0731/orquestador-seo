# 02 · Contratos de estado y routing (mapa vivo, NO contrato congelado)

Este documento hace **legible** el estado compartido que las skills del ecosistema producen y
consumen de facto, y es el **mapa de routing** del orquestador. **No fija fronteras**: las describe
con un *estado* explícito. La frontera correcta solo se valida ejecutando.

> **Cómo leerlo.** El estado evita que esto osifique:
> - 🟢 **firme** — invariante o validada por ejecución; el corte no está en duda.
> - 🟡 **en prueba** — frontera bajo experimento activo; **no** tratar como cerrada.
> - 🔴 **de riesgo** — vigilar; primera candidata a re-cortar si la ejecución lo pide.

El estado compartido es la fuente única de verdad. Las skills de diseño lo escriben; las de
ejecución lo leen. El orquestador solo gestiona estado, gates de dependencia y la arista de retorno
(`SKILL.md` → *Regla de conflicto*). No toma decisiones de SEO.

---

## 0. Sustrato: la carpeta-contrato canónica

Raíz única por cliente: `C:\Users\Marke\Documents\Respaldo SEO\Clientes\<slug>\`. Las skills se
comunican dejando/leyendo archivos ahí y tablas en `data\seo.db` — **no se llaman entre sí**.
No hay repo git por cliente ni registro central: la carpeta + `seo.db` son la verdad.

Artefactos clave: `conexiones\conexiones.json` (seo-setup), `data\seo.db` (setup crea, sync puebla),
`base\contexto-<slug>.md` (base-cliente), `sitio.yaml` (arquitectura-seo emite),
`arquitectura\resultados\*.csv` + `arquitectura\data\enfoque.md`, `schema-graph\`, `resultados\`
(interlinking), `cwv\` (seo-vitals), `geo\` (geo-audit), `linkbuilding\`, `secciones\<slug-pagina>\`,
`docs\superpowers\{discovery,specs,plans}\` (master).

---

## 1. Mapa producto/consumo por skill (normalizado a `Clientes\<slug>\`)

| Skill | LEE (input) | ESCRIBE (output) |
|---|---|---|
| **base-cliente** | docs/fuentes; opcional crawl de extraccion | `base\` + `base\contexto-<slug>.md` |
| **seo-setup-cliente** | credenciales (google-api.json, ~/.claude.json) | `conexiones\conexiones.json` + `data\seo.db` |
| **seo-sync** | conexiones.json + APIs (GSC/GA4/Clarity/DataForSEO) | puebla `seo.db` |
| **extraccion** | `sitio.yaml`, plantillas SF | `data\*.csv` + `page_source\` + `historico\<fecha>\` |
| **arquitectura-seo** | `base\`, `conexiones\`, `keywords\`, seo.db (GSC), `arquitectura\data\enfoque.md`, `inventario\` (si existe) | `arquitectura\resultados\{arquitectura.csv, enlazado.csv, mapeo-301.csv, mapa-keywords.csv}` + `sitio.yaml` |
| **seo-master-plan** | `contexto-<slug>.md`, `arquitectura.csv`, `enlazado.csv`, `mapa-keywords.csv`, bundle de diagnóstico | FRAMING: `arquitectura\data\enfoque.md`; ENSAMBLAJE: `docs\superpowers\{specs,plans}\` |
| **diseno-secciones** | `keywords\`, seo.db, `page_source\` | `secciones\<slug-pagina>\{01,02,03}` |
| **schema-graph** | `data\*.csv` + `page_source\` | `schema-graph\` (snippets, SCHEMA-REPORT.md) |
| **interlinking** | `data\all_inlinks.csv` + `internal_html.csv`, `sitio.yaml` | `resultados\` (métricas, grafos, tablero) |
| **html-semantico** | HTML/URL ad hoc | informe en chat |
| **content-engine** | tema/.docx, KB (silo aparte) | `Herramientas\SEO Blogs\content-engine\proyectos\<empresa>\` |
| **seo-vitals** | `conexiones`/`sitio.yaml`, `data\*.csv`, CrUX | `cwv\seo.db` + `informes\CWV-<fecha>.md` |
| **seo-analisis** | seo.db + crawl + DinoRank | tabla `hallazgos` + `analisis\*.md` |
| **seo-analisis-gsc** | seo.db (GSC) + HTTP propio | tabla `url_status` + reportes chat |
| **seo-cambios** | seo.db (GSC/GA4/Clarity/ranks) | tabla `changes_log` + veredictos |
| **seo-dashboard** | seo.db (hallazgos, url_status, GSC) + clusters.json | `reportes\dashboard.html` + `clusters.json` |
| **linkbuilding** | conexiones, GSC CSV, clusters.json, DinoRank/DataForSEO | tabla `lb_backlinks` + pipeline + `linkbuilding\informes\` |
| **geo-audit** | seo.db, `geo\geo.yaml`, `base\`, `sitio.yaml` | hallazgos GEO + scores en seo.db + `geo\informes\` |
| **claude-seo:*** (gap-filler) | ad hoc | content-brief, auditoría single-page, hreflang |

---

## 2. Secuencias por escenario

**Espina común** (master partido alrededor de arquitectura-seo):
`base-cliente → master FRAMING (§0–§2 → enfoque.md) → arquitectura-seo → master ENSAMBLAJE (§3–§14)`.

**Escenario A — existente** (diagnosticar → rediseñar → migrar → monitorear):
```
0. seo-setup-cliente + base-cliente
1. seo-sync BACKFILL ∥ extraccion crawl
2. DIAGNÓSTICO: seo-analisis · seo-analisis-gsc · interlinking · schema-graph · geo-audit · seo-vitals · linkbuilding
3. master FRAMING → enfoque.md
4. arquitectura-seo (migración → arquitectura.csv + mapeo-301.csv)
5. master ENSAMBLAJE → spec MIGRACIÓN + planes
6. Ejecución: diseno-secciones · content-engine · schema-graph
7. MONITOREO: seo-cambios · seo-dashboard · re-auditorías · seo-sync diario
```

**Escenario B — greenfield** (diseñar → construir → validar → lanzar → monitorear):
```
0. seo-setup-cliente + base-cliente
1. master FRAMING → enfoque.md
2. arquitectura-seo (greenfield: estudio de kws → árbol, sin inventario/301)
3. master ENSAMBLAJE → spec FOUNDATION + planes
4. BUILD: diseno-secciones · content-engine · schema-graph
5. VALIDACIÓN pre-launch: seo-vitals · geo-audit · interlinking · html-semantico
6. LAUNCH: sitemap + IndexNow
7. MONITOREO: seo-sync captura → converge con A
```
A y B convergen en el **monitoreo con seo.db poblado**.

---

## 3. Capas de frontera (dónde vive cada responsabilidad)

### Capa 1 — Diseño (gate secuencial)

| # | Responsabilidad | Dónde vive hoy | Entrega (clave) | Frontera |
|---|---|---|---|---|
| 1 | Mapeo negocio + intención | master FRAMING §0–§1 + base-cliente | `enfoque.md` + params de nicho | 🟢 firme |
| 2 | Dimensionamiento de demanda | arquitectura-seo (estudio de kws) | `mapa-keywords.csv` (volúmenes + veredicto) | 🟢 firme |
| 3 | Taxonomía / tipos de página | arquitectura-seo → `arquitectura.csv` | árbol de tipos de página | 🟡 **en prueba (3↔5)** |
| 4 | Convención de URLs | arquitectura-seo (plegada en 3) | patrón por clase de URL | 🟢 firme |
| 5 | Enlazado interno | arquitectura-seo `enlazado.csv` · interlinking (verificación) | reglas de enlace + flujo a money pages | 🟡 **en prueba (3↔5)** |

### Gate — Medición (entre diseño y ejecución)

| # | Responsabilidad | Dónde vive hoy | Hecho | Frontera |
|---|---|---|---|---|
| 10 | Medición e instrumentación | seo-setup-cliente (crea `changes_log`) · seo-sync (baseline) · seo-cambios (`report.py beforeafter`) · seo-dashboard | todo cambio rastreable a un veredicto | 🟢 firme (gate, no fase final) |

### Capa 2 — Ejecución (loops concurrentes)

| # | Responsabilidad | Dónde vive hoy | Frontera |
|---|---|---|---|
| 6 | Rastreo / indexabilidad | extraccion (crawl) · arquitectura-seo · seo-analisis-gsc + **modo de render** | 🔴 de riesgo (render enreda 6↔8) |
| 7 | Contenido + on-page (+schema) | diseno-secciones · content-engine · schema-graph · seo-analisis | 🟢 firme |
| 8 | Rendimiento | seo-vitals (CWV, CrUX) + **modo de render** | 🔴 de riesgo (render enreda 6↔8) |
| 9 | Off-page / autoridad | linkbuilding | 🟢 firme (track separado continuo) |

### Capa 3 — Gobierno continuo

| # | Responsabilidad | Dónde vive hoy | Frontera |
|---|---|---|---|
| 11 | Revisión de obsolescencia | decision gates del master + seo-cambios (regresión) + seo-dashboard | 🟡 mecanismo añadido, sin correr aún |

---

## 4. Costuras transversales (no son skills; varias las heredan)

- **Modo de render (CSR/SSR/estático)** → decisión de arquitectura que **6 y 8 heredan a la vez**.
  Se decide en discovery (master FRAMING) y se declara en `sitio.yaml`/spec. Por eso 6 y 8 están 🔴.
  Crítico en sitios programáticos: si las páginas a escala son CSR, la % de indexación se desploma.
- **i18n** → módulo condicional; único caso gap-filler estructural → `claude-seo:seo-hreflang`.

---

## 5. La frontera 🟡 en prueba: taxonomía (3) ↔ enlazado (5)

Frontera de **mayor riesgo**, **no se decide en escritorio**. Hoy `arquitectura.csv` (taxonomía) y
`enlazado.csv` los emite arquitectura-seo; interlinking verifica el enlazado real contra ellos.
Pregunta abierta: ¿taxonomía y enlazado son un corte o dos?

**Cómo se resuelve (instrumentado):** experimento en **metalectro** (6 clusters, baselines
congelados). Cada cambio se registra con seo-cambios (`logchange --area taxonomia|enlazado|ambos`);
el veredicto sale de `report.py --report covariacion`:
- ≥60% de cambios tocó `ambos` → **fusionar 3 y 5** (🟢).
- ≤30% → **mantener separadas** (🟢).
- zona gris → seguir 🟡, registrar más.

Cuando el veredicto llegue con alta confianza, **se actualiza este documento y el spec**.

---

## 6. Qué NO es este documento

- **No** es la re-arquitectura a contrato tipado de las skills (se difiere hasta que la ejecución
  valide las fronteras).
- **No** congela ningún corte: las filas 🟡/🔴 son provisionales.
- **No** reemplaza a los SKILL.md: los mapea, para que el orquestador valide handoffs.
