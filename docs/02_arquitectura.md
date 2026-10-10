# 02 · Arquitectura

```mermaid
flowchart LR
  subgraph SRC[Fuente]
    FM[Freddie Mac<br/>Sample Dataset<br/>ficheros .txt delimitados por '|']
  end

  subgraph LOCAL[Tu PC]
    DL[Descarga manual]
    PR[Productor Kafka<br/>Python]
    KL[(Kafka local<br/>Docker)]
    BR[Bridge consumidor<br/>Plan B]
  end

  subgraph KAFKA[Kafka gestionado]
    KC[(Topics:<br/>loan_applications<br/>loan_performance)]
  end

  subgraph DBX[Databricks Free Edition · Unity Catalog]
    VOL[/Volume landing/]
    B[(Bronze<br/>datos crudos + metadatos)]
    S[(Silver<br/>tipado, limpio, validado)]
    G[(Gold<br/>features, target,<br/>agregados, scores)]
    DQ[(Tabla de métricas<br/>de calidad)]
    ML[MLflow<br/>entrenamiento + registro]
    WH[SQL Warehouse 2X-Small]
  end

  PBI[Power BI Desktop]

  FM --> DL --> VOL
  VOL -- Auto Loader --> B
  PR --> KL
  PR --> KC
  KC -- Structured Streaming<br/>si hay salida a internet --> B
  KL --> BR -- REST API / SDK --> VOL
  B --> S --> G
  S -.-> DQ
  G --> ML --> G
  G --> WH --> PBI
  DQ --> WH
```

## Por qué esta arquitectura

| Decisión | Por qué | Alternativa descartada |
|---|---|---|
| **Medallion (bronze/silver/gold)** | Separa "lo que llegó" de "lo que es fiable" de "lo que consume negocio". Si una regla de limpieza está mal, reprocesas desde bronze sin volver a descargar. | Una sola tabla limpia: rápida, pero pierdes trazabilidad y reproceso. |
| **Delta Lake** | Transacciones ACID, esquema forzado, *time travel* (útil para comparar antes/después de limpiar), `MERGE` para actualizaciones mensuales. Es el formato nativo en Databricks. | Parquet a pelo: sin ACID ni MERGE. |
| **"BD cloud" = tablas Delta gestionadas en Unity Catalog** | Free Edition no permite ubicaciones de almacenamiento propias; las tablas gestionadas *son* tu almacenamiento cloud y el SQL Warehouse las sirve como una BD. | Postgres/Azure SQL externo: coste y problemas de red desde serverless. |
| **Auto Loader para batch** | Mismo motor (Structured Streaming) que usarás con Kafka → aprendes un único modelo mental: *fuente → stream → Delta*. Procesa solo ficheros nuevos. | `spark.read.csv` una vez: vale para explorar, no enseña ingesta incremental. |
| **Bridge como plan B de streaming** | La salida a internet de Free Edition es una lista blanca no publicada. Si Databricks no alcanza el broker, el bridge sigue dejándote practicar productores, consumidores, offsets y particiones. | Renunciar a Kafka. |
| **Power BI en modo Import** | El warehouse gratuito es pequeño; Import evita tenerlo encendido en cada clic. | DirectQuery: más "real", pero lento y consume cuota. |

## Modelo de datos (borrador — se concreta en Fase 4)
- `bronze.origination_raw`, `bronze.performance_raw`, `bronze.kafka_events_raw`
- `silver.loans` (1 fila por préstamo), `silver.loan_monthly` (1 fila por préstamo-mes)
- `gold.loan_features` (features + `default_24m`), `gold.loan_scores`,
  `gold.portfolio_risk_by_vintage_state`, `gold.dq_metrics`

## Convenciones
- Catálogo `credit_risk`, esquemas `bronze`, `silver`, `gold`, volumen `credit_risk.bronze.landing`.
- Nombres en `snake_case` inglés (código) — documentación en español.
- Todo registro en bronze lleva `_ingest_ts`, `_source_file`, `_batch_id`.
