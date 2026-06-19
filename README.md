# orquestador-seo

Skill **conductor** de Claude Code: el punto de entrada único de un proyecto SEO completo. No hace el trabajo — lo **enruta** a los skills adecuados a lo largo de las 6 fases del ciclo.

## Qué hace

Conduce el ciclo de vida de una estrategia SEO combinando dos skills complementarios:

| Skill | Aporta |
|---|---|
| [`seo-master-plan`](https://github.com/dasv0731/seo-strategy) | **QUÉ y POR QUÉ** — estrategia, estructura, spec §0–§14, planes, KPIs |
| [`claude-seo`](https://github.com/AgriciDaniel/claude-seo) | **QUÉ ES HOY y SI FUNCIONA** — datos en vivo, auditorías, schema, técnico, local, GEO |

### Las 6 fases

```
0 · Intake & baseline   discovery + auditoría del sitio actual + competidores
1 · Research            keywords/clusters con volúmenes reales
2 · Design Spec         spec §0–§14 informado por datos, validado en vivo
3 · Planes              planes ejecutables + briefs de contenido
4 · Build validation    validar implementación contra el spec antes de lanzar
5 · Launch & monitor    KPIs + drift + decision gates → volver a fase 1/2
```

El núcleo es la **regla de conflicto**: cuando los datos de `claude-seo` contradicen un supuesto de `seo-master-plan`, ganan los datos y se ajusta la estrategia.

## Dependencias

Ambos skills deben estar instalados:

- **`seo-master-plan`** en `~/.claude/skills/seo-master-plan/`
- **`claude-seo`** en `~/.claude/skills/claude-seo/` (se auto-carga como `claude-seo@skills-dir`)

## Instalación

```bash
# El orquestador
git clone https://github.com/dasv0731/orquestador-seo.git ~/.claude/skills/orquestador-seo

# Dependencia 1: estrategia
git clone https://github.com/dasv0731/seo-strategy.git ~/.claude/skills/seo-master-plan

# Dependencia 2: datos/análisis en vivo (se carga como claude-seo@skills-dir)
git clone https://github.com/AgriciDaniel/claude-seo.git ~/.claude/skills/claude-seo
```

En Windows (PowerShell), reemplaza `~/.claude` por `"$env:USERPROFILE\.claude"`.

Verifica con `claude plugin list` que aparezca `claude-seo@skills-dir: loaded`.

## Modo multi-cliente (agencia)

Para llevar el SEO de varias cuentas, cada cliente es un **proyecto aislado**: su propia carpeta, memoria, bitácora, estudios de KW, conexiones (GSC/GA4/DataForSEO) y repo privado.

- Workspace raíz `~/seo-clientes/` con `REGISTRO.md` + plantilla en `plantilla-cliente/`.
- **Memoria aislada por cliente** automáticamente (la memoria nativa de Claude Code se indexa por ruta de proyecto).
- **Credenciales compartidas a nivel agencia** (un service account + 1 login DataForSEO); cada cliente solo aporta *qué* propiedad usar. Setup: `references/01-credenciales-agencia.md`.
- Alta de cliente (Fase 0.0): `references/00-alta-cliente.md`.

## Uso

Se activa al iniciar un proyecto SEO de principio a fin, o ante frases como *"orquestar SEO"*, *"proyecto SEO completo"*, *"estrategia + auditoría SEO"*, *"dar de alta un cliente"*.
