# 00 · Plan de aprendizaje

> Documento vivo. Es el mapa del proyecto: qué se aprende en cada fase, en qué orden y cuándo se da por
> terminada. Una fase solo se cierra cuando el usuario escribe **"Hemos acabado la Fase X"**.

## 1. Análisis: objetivo vs. planteamiento original

**Objetivo:** aprender, de forma progresiva y con un mismo proyecto, las herramientas de ingeniería de
datos más demandadas que aún no conoces (ya dominas SQL/Snowflake y Power BI).

**Lo que estaba bien en el planteamiento original** (se mantiene): Databricks + Delta + medallion,
Kafka con plan B, MLflow, Power BI, los ADR y la idea de documentar las decisiones.

**Lo que cambia y por qué:**

| Cambio | Motivo |
|---|---|
| Se añade una **Fase 0 de entorno** de verdad (git, Python, Docker, Dev Container, calidad de código) | Partes de Python básico y Docker casi nulo; sin un entorno sólido, cada fase se atasca en problemas de instalación. |
| **DuckDB antes de Spark** para explorar | Exploras los datos con SQL (tu punto fuerte) y aprendes Python sin pelearte a la vez con Spark. |
| **Spark local antes de Databricks** | Primero entiendes Spark (lazy evaluation, particiones, plan de ejecución) en tu PC, gratis y sin límites. Después Databricks se entiende como "Spark gestionado + extras", que es lo que es. |
| **dbt para la capa gold** | Es de las herramientas más pedidas en ofertas y encaja con tu SQL. Funciona igual sobre Snowflake: lo que aprendas lo puedes usar en tu trabajo. |
| **Airflow** para orquestar | El orquestador más extendido del mercado. Antes no estaba en el stack. |
| **CI/CD con GitHub Actions + entornos dev/prod** | Es lo que separa un proyecto de portfolio de un "notebook que funciona en mi máquina". Cubre las pruebas pre-producción que pediste. |
| **Calidad de datos y tests repartidos por fases** | En lugar de una fase aislada, cada fase añade sus propias pruebas (pytest → expectations → tests de dbt → integración). |
| Power BI se queda en **una fase corta** | Ya lo dominas; solo se aprende la conexión a Databricks y el modelado sobre gold. |

## 2. Cómo trabajaremos desde VS Code

```
 Tu PC (Windows)                                        Nube
 ┌──────────────────────────────────────────┐      ┌──────────────────────────┐
 │ VS Code                                  │      │ Databricks Free Edition  │
 │  ├─ Dev Container (Docker)               │      │  Unity Catalog, Volumes  │
 │  │   Python 3.12 + Java 17 + PySpark     │      │  Serverless, SQL WH      │
 │  │   + Delta + pytest + dbt + ruff       │ ───► │  Jobs, MLflow            │
 │  │   (Spark local para aprender y tests) │ DAB  └──────────────────────────┘
 │  ├─ Extensión Databricks + CLI           │      ┌──────────────────────────┐
 │  └─ Git ──────────────────────────────── │ ───► │ GitHub (repo + Actions)  │
 │ Docker Compose (solo en su fase):        │      └──────────────────────────┘
 │   Kafka (Fase 6) · Airflow (Fase 5)      │      ┌──────────────────────────┐
 │ Power BI Desktop (Fase 8)                │ ◄─── │ Aiven Kafka (opcional)   │
 └──────────────────────────────────────────┘      └──────────────────────────┘
```

**Por qué un Dev Container** y no instalar todo en Windows:
- Spark en Windows nativo necesita `winutils.exe`/`hadoop.dll` y da errores raros. En Linux funciona sin más.
- El entorno queda escrito en un fichero (`.devcontainer/`). Cualquiera, incluido un reclutador, lo
  reproduce con un clic. Es así como trabajan muchos equipos.
- Aprendes Docker usándolo de verdad, no en un tutorial aparte.
- Python 3.12 dentro del contenedor, la misma versión que el serverless de Databricks. Fuera de él
  conservas tu Python 3.13 intacto.

**Por qué Databricks Asset Bundles (DAB)**: el código vive en el repo y se despliega a Databricks
con un comando (`databricks bundle deploy`). Se acaba lo de "copiar y pegar en notebooks de la web",
y de paso tienes la base para el CI/CD y los entornos dev/prod.

**Límite de 7,6 GB de RAM**: Docker Desktop tendrá un límite de unos 4–5 GB y en cada fase solo se
arranca lo que hace falta.

## 3. Formato de cada fase
Todas las fases siguen el mismo ciclo:

1. **Planificación**: objetivos, conceptos que hay que entender y recursos (documentación oficial primero).
2. **Teoría mínima**: explicación del concepto, comparado con SQL/Snowflake cuando aplique.
3. **Implementación**: Claude prepara el andamiaje; tú escribes la lógica central (esqueletos con `TODO`);
   Claude la revisa.
4. **Pruebas**: no se cierra ninguna fase sin tests que pasen.
5. **Cierre**: `docs/aprendizaje/fase-XX.md` con conceptos, comandos, errores y preguntas de entrevista,
   y después la frase **"Hemos acabado la Fase X"**.

## 4. Mapa de fases

Estimación con ~4 h/semana (orientativa; lo que manda es el criterio de cierre, no la fecha):

| # | Fase | Tecnologías nuevas | Semanas aprox. | Estado |
|---|---|---|---|---|
| 0 | Entorno y fundamentos | Git/GitHub, Docker, Dev Containers, uv, ruff, pytest, pre-commit | 2 | 🔄 en curso |
| 1 | Datos y exploración | Python para datos, DuckDB, Jupyter | 2 | ⬜ |
| 2 | Fundamentos de Spark (local) | PySpark, Spark UI, Delta Lake | 3 | ⬜ |
| 3 | Lakehouse en Databricks: bronze y silver | Databricks, Unity Catalog, Auto Loader, Lakeflow Declarative Pipelines, Asset Bundles | 3–4 | ⬜ |
| 4 | Capa gold con dbt | dbt Core, dbt-databricks, modelado dimensional, tests y docs de dbt | 3 | ⬜ |
| 5 | Orquestación | Databricks Jobs, Apache Airflow | 2–3 | ⬜ |
| 6 | Streaming | Apache Kafka, Spark Structured Streaming | 3 | ⬜ |
| 7 | Machine Learning | scikit-learn, LightGBM, MLflow | 3 | ⬜ |
| 8 | Visualización | Power BI ↔ Databricks SQL Warehouse | 1 | ⬜ |
| 9 | CI/CD y pruebas pre-producción | GitHub Actions, entornos dev/prod, tests de integración y E2E | 2–3 | ⬜ |
| 10 | Recapitulación y mejoras futuras | — | 1 | ⬜ |

Total ≈ 25–28 semanas (~6 meses).

---

## Fase 0 · Entorno y fundamentos
**Objetivo:** un entorno reproducible en el que todo funcione antes de tocar datos.

**Conceptos:** control de versiones y flujo de ramas; imagen vs. contenedor vs. volumen en Docker;
Dev Containers; entornos virtuales y gestión de dependencias (`pyproject.toml`, `uv`); linting y
formateo; tests unitarios; hooks de pre-commit.

**Implementación:**
1. Arreglar `JAVA_HOME`, arrancar Docker Desktop y limitar su memoria (WSL `.wslconfig`).
2. `git init`, primer commit, repo en GitHub (público, sin datos ni secretos).
3. Instalar `uv`; crear `pyproject.toml` con el paquete `credit_risk`.
4. `.devcontainer/` con Python 3.12 + Java 17 + PySpark; abrir el proyecto dentro del contenedor.
5. `ruff` (lint + formato), `pytest` y `pre-commit`.
6. Crear cuentas: **Databricks Free Edition**, **Freddie Mac** (registro para el dataset) y GitHub si no la tienes.
7. Instalar Databricks CLI + extensión de VS Code y autenticarse.

**Pruebas:** dentro del contenedor, `pytest` pasa un test trivial que crea una `SparkSession` local;
`databricks current-user me` devuelve tu usuario; el hook de pre-commit bloquea un fichero mal formateado.

**Criterio de cierre:** clonar el repo en limpio y abrirlo en el Dev Container funciona sin pasos manuales.

## Fase 1 · Datos y exploración
**Objetivo:** entender los datos de Freddie Mac y fijar la definición del target `default_24m`.

**Conceptos:** layout de los ficheros (origination vs. monthly performance, separador `|`, sin
cabecera); cosechas (vintages); códigos de morosidad; data leakage; perfilado de datos.

**Implementación:**
1. Descargar el *Sample Dataset* de 3–4 cosechas (p. ej. 2016–2019, más 2020 como test COVID).
2. Módulo `src/credit_risk/ingestion/schemas.py` con los esquemas de ambos ficheros (sacados de la guía oficial).
3. Exploración con **DuckDB** en un notebook: nulos, rangos, cardinalidad, distribución de
   morosidad por cosecha. Tu SQL te sirve aquí directamente.
4. Construir el target en SQL y medir la tasa de impago por cosecha.
5. Actualizar `docs/01_definicion_proyecto.md` con la definición final del target.

**Pruebas:** tests de que el esquema lee una muestra sintética (`data/sample/`) con el número de
columnas y tipos esperados; test del cálculo del target sobre casos inventados (un préstamo que
entra en 90+ el mes 23 → 1; el mes 25 → 0).

**Criterio de cierre:** sabes explicar cada columna que usarás y por qué el target está bien definido.

## Fase 2 · Fundamentos de Spark (local)
**Objetivo:** entender Spark de verdad antes de usarlo en la nube.

**Conceptos:** qué problema resuelve Spark (procesamiento distribuido); driver/executors;
DataFrame API; transformaciones vs. acciones; *lazy evaluation*; particiones y *shuffle*;
plan de ejecución (`explain()`); Spark UI; Spark SQL; Delta Lake (log transaccional, ACID,
`MERGE`, time travel, schema enforcement/evolution).

**Implementación:**
1. Notebook de aprendizaje: la misma consulta de la Fase 1 en DuckDB, en Spark SQL y en la DataFrame API.
2. Leer los ficheros crudos con esquema explícito y añadir las columnas de metadatos de bronze.
3. Escribir en Delta local; probar `MERGE` con una actualización simulada y *time travel*.
4. Funciones de transformación puras y testeables en `src/credit_risk/transformations/`.

**Pruebas:** `pytest` con una `SparkSession` local compartida (fixture) y DataFrames pequeños
construidos en el test.

**Criterio de cierre:** sabes leer un `explain()` y explicar cuándo hay *shuffle* y por qué cuesta.

## Fase 3 · Lakehouse en Databricks: bronze y silver
**Objetivo:** llevar lo aprendido a la plataforma, con ingesta incremental y calidad de datos.

**Conceptos:** workspace, Unity Catalog (catálogo.esquema.tabla), Volumes, tablas gestionadas;
Auto Loader (`cloudFiles`, checkpoints, schema inference/evolution); Lakeflow Declarative Pipelines
y *expectations*; Asset Bundles (targets, recursos).

**Implementación:**
1. Bundle `databricks.yml`; catálogo, esquemas y volumen `landing` creados por código.
2. Subir ficheros al Volume con la CLI.
3. Bronze con Auto Loader, usando el código del paquete `credit_risk`.
4. Silver (`silver.loans`, `silver.loan_monthly`): tipado, limpieza, deduplicación y reglas de calidad
   como *expectations*; métricas en `gold.dq_metrics`.
5. ADR-003 (entorno de desarrollo) y ADR-004 (dónde viven las reglas de calidad).

**Pruebas:** tests unitarios de las reglas (local); despliegue con `bundle validate` / `bundle deploy`;
reejecutar la ingesta no duplica filas (idempotencia).

**Criterio de cierre:** subes un fichero nuevo, ejecutas el pipeline y solo se procesa ese fichero.

## Fase 4 · Capa gold con dbt
**Objetivo:** modelado analítico con la herramienta estándar del mercado.

**Conceptos:** ELT vs. ETL; proyecto dbt (models, sources, refs, macros, Jinja); materializaciones
(view, table, incremental); tests genéricos y singulares; documentación y *lineage*; modelado
dimensional (hechos/dimensiones) para Power BI.

**Implementación:** proyecto `dbt/` con `dbt-databricks` apuntando al SQL Warehouse:
`gold.loan_features` (features + target), `gold.portfolio_risk_by_vintage_state`, dimensiones
para el dashboard. Al menos un modelo incremental.

**Pruebas:** `dbt build` (modelos + tests); tests de unicidad, no nulos, relaciones y rangos
aceptados; un test singular que verifica que ninguna feature procede del fichero de rendimiento (anti-leakage).

**Criterio de cierre:** `dbt docs` muestra el linaje completo desde las fuentes silver hasta gold.

## Fase 5 · Orquestación
**Objetivo:** que el pipeline se ejecute solo, en orden y con reintentos.

**Conceptos:** DAG, dependencias, idempotencia, reintentos, *backfill*, programación (cron);
diferencias entre orquestador nativo (Databricks Jobs) y orquestador externo (Airflow).

**Implementación:**
1. Job de Databricks definido en el bundle: bronze → silver → dbt (task de dbt).
2. Airflow en Docker local (modo ligero), con un DAG que dispara el job de Databricks usando el
   provider oficial y espera a que termine.
3. ADR-005: cuándo usar cada orquestador.

**Pruebas:** test de integridad del DAG (se importa sin errores, sin ciclos); ejecución de un
fallo provocado para comprobar que los reintentos y las alertas funcionan.

**Criterio de cierre:** sabes defender en una entrevista por qué Airflow, y por qué a veces no.

## Fase 6 · Streaming con Kafka
**Objetivo:** ingesta de eventos en tiempo (casi) real.

**Conceptos:** broker, topic, partición, offset, consumer group, *delivery semantics*
(at-least-once / exactly-once), serialización (JSON vs. Avro), KRaft; Structured Streaming
(micro-batch, checkpoint, watermark, trigger).

**Implementación:**
1. Kafka KRaft en Docker Compose y CLI de Kafka para crear topics y consumir a mano.
2. Productor Python (`streaming/producer/`) que emite solicitudes nuevas y actualizaciones mensuales.
3. Prueba de conectividad desde Databricks (decide el ADR-002).
4. Plan A (Aiven + `readStream.format("kafka")`) o plan B (bridge → Volume → Auto Loader).
   Confluent Cloud queda fuera: su trial dura 30 días, demasiado poco para 4 h/semana.
   Se apunta como mejora futura (Schema Registry/Avro).
5. Las actualizaciones aplican `MERGE` sobre silver.

**Pruebas:** tests del productor (formato del evento validado con un esquema); prueba de
reprocesamiento (parar, reiniciar y comprobar que no se pierden ni se duplican eventos).

**Criterio de cierre:** un GIF o vídeo corto del flujo productor → Kafka → lakehouse funcionando.

## Fase 7 · Machine Learning
**Objetivo:** modelo de probabilidad de impago trazable y reproducible.

**Conceptos:** split temporal (por cosecha, no aleatorio); clases desbalanceadas; baseline
logístico vs. boosting; métricas (AUC, PR-AUC, KS, calibración, coste por umbral);
MLflow (tracking, model registry en Unity Catalog); scoring por lotes.

**Implementación:** features desde `gold.loan_features`; tres modelos comparados en MLflow;
registro del mejor modelo; job de scoring que escribe `gold.loan_scores` con bandas de riesgo.

**Pruebas:** test de que el pipeline de features no usa columnas prohibidas; test de que el
modelo registrado carga y puntúa un lote pequeño; umbral mínimo de AUC en el conjunto de test.

**Criterio de cierre:** sabes explicar al negocio qué significa la banda "alto" y cuánto se equivoca el modelo.

## Fase 8 · Visualización con Power BI
**Objetivo:** conectar tu herramienta conocida a un lakehouse.

**Conceptos:** conector Databricks (hostname, HTTP path, token); Import vs. DirectQuery
frente a un warehouse pequeño; modelo en estrella sobre gold.

**Implementación:** 3 páginas (cartera, calidad del modelo, calidad de datos); capturas en `powerbi/`.

**Pruebas:** cuadre de las cifras del dashboard con consultas SQL directas al warehouse.

## Fase 9 · CI/CD y pruebas pre-producción
**Objetivo:** tratar el proyecto como si fuese a pasar a producción.

**Conceptos:** pirámide de tests (unitarios → integración → end-to-end); CI vs. CD; entornos
dev/prod con *targets* del bundle; secretos en GitHub; *smoke tests*; contratos de datos;
pruebas de regresión de datos.

**Implementación:**
1. GitHub Actions en cada PR: `ruff`, `pytest`, `bundle validate`, `dbt build` contra dev.
2. Despliegue a `prod` (esquemas separados) al fusionar en `main`.
3. Test end-to-end: un fichero de muestra conocido recorre todo el pipeline y se comparan los
   resultados de gold con valores esperados.
4. Checklist de "paso a producción" (`docs/04_checklist_produccion.md`).

**Criterio de cierre:** un PR con un bug provocado se bloquea en CI; uno correcto se despliega solo.

## Fase 10 · Recapitulación y mejoras futuras
- `docs/05_recapitulacion.md`: cada tecnología, para qué sirve, cuándo usarla y cuándo no, y dónde
  aparece en el proyecto.
- README final con diagrama, capturas y cómo ejecutarlo.
- **Mejoras futuras** (para evaluar entonces): Terraform (infraestructura como código),
  Apache Iceberg frente a Delta, portar dbt a Snowflake, Great Expectations / Soda, Schema Registry y
  Avro (Confluent), Apache Flink, *feature store*, model serving, observabilidad de datos,
  monitorización de *drift* del modelo, orquestadores alternativos (Dagster, Prefect), cloud AWS/Azure.

---

## Registro de cierres
| Fase | Fecha de cierre | Resumen |
|---|---|---|
| 0 | — | — |
