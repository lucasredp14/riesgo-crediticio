# Riesgo Crediticio — Lakehouse end-to-end con Kafka, Spark y Databricks

Proyecto personal de aprendizaje/portfolio. Pipeline de datos completo que ingiere hipotecas reales
(Freddie Mac Single-Family Loan-Level Dataset), las valida y modela en un lakehouse Delta
(bronze/silver/gold), entrena un modelo de **probabilidad de impago** y expone los resultados en Power BI.

> ⚠️ No es un sistema de decisión real. Es un ejercicio técnico sobre datos públicos.

## Pregunta de negocio
*Dado el perfil de un préstamo en el momento de su concesión, ¿qué probabilidad hay de que llegue a
90+ días de impago en sus primeros 24 meses?* → segmentación en bandas de riesgo (bajo / vigilar / alto).

## Stack
| Capa | Tecnología |
|---|---|
| Entorno | Git/GitHub, Docker + Dev Containers, uv, ruff, pytest, pre-commit |
| Exploración | DuckDB, Jupyter |
| Ingesta batch | Databricks Free Edition (Volumes + Auto Loader, PySpark) |
| Ingesta streaming | Apache Kafka (Docker local + broker gestionado gratuito) → Spark Structured Streaming |
| Almacenamiento | Delta Lake en Unity Catalog, arquitectura medallion |
| Transformación gold | dbt Core (dbt-databricks) |
| Calidad de datos | Reglas PySpark + expectations en Lakeflow Declarative Pipelines + tests de dbt |
| Orquestación | Databricks Jobs + Apache Airflow |
| ML | scikit-learn / LightGBM + MLflow |
| BI | Power BI Desktop vía SQL Warehouse |
| CI/CD | GitHub Actions + Databricks Asset Bundles (entornos dev/prod) |

## Estructura
Las carpetas se crean fase a fase, cuando hacen falta.

```
├── docs/                  Documentación (plan, definición, arquitectura, ADR)
├── data/                  Datos locales (ignorados por git)
│   ├── raw/               Ficheros originales de Freddie Mac
│   └── sample/            Muestras reducidas para pruebas
├── conf/                  Configuración (*.example.yaml en git; los reales no)
└── tests/                 Tests unitarios
```

Previstas (ver [plan](docs/00_plan_aprendizaje.md)): `src/credit_risk/` (paquete Python, Fase 0–1),
`notebooks/` (Fase 1), `databricks.yml` (Fase 3), `dbt/` (Fase 4), `airflow/` (Fase 5),
`streaming/` (Fase 6), `powerbi/` (Fase 8), `.github/workflows/` (Fase 9).

## Estado por fases
Detalle de cada fase en el [plan de aprendizaje](docs/00_plan_aprendizaje.md).

- [ ] Fase 0 — Entorno y fundamentos
- [ ] Fase 1 — Datos y exploración (DuckDB)
- [ ] Fase 2 — Fundamentos de Spark (local)
- [ ] Fase 3 — Lakehouse en Databricks: bronze y silver
- [ ] Fase 4 — Capa gold con dbt
- [ ] Fase 5 — Orquestación (Databricks Jobs + Airflow)
- [ ] Fase 6 — Streaming con Kafka
- [ ] Fase 7 — Machine Learning (MLflow)
- [ ] Fase 8 — Dashboard Power BI
- [ ] Fase 9 — CI/CD y pruebas pre-producción
- [ ] Fase 10 — Recapitulación y mejoras futuras

## Documentación
- [Plan de aprendizaje](docs/00_plan_aprendizaje.md)
- [Definición del proyecto](docs/01_definicion_proyecto.md)
- [Arquitectura](docs/02_arquitectura.md)
- [Restricciones de la capa gratuita](docs/03_restricciones_free_tier.md)
- [Registro de decisiones (ADR)](docs/decisiones/)

## Datos
Freddie Mac Single-Family Loan-Level Dataset. Requiere registro y aceptación de sus términos; los datos
**no se redistribuyen** en este repositorio.
