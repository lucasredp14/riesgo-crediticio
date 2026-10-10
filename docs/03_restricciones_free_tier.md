# 03 · Restricciones de la capa gratuita y su impacto en el diseño

*Revisado: septiembre 2026. Estas condiciones cambian: vuelve a comprobarlas antes de cada fase.*

## Databricks Free Edition
| Límite | Impacto en el proyecto |
|---|---|
| Solo **serverless** para notebooks, tamaño/uso limitados | Trabajar con la *muestra* de Freddie Mac, no con el dataset completo. Nada de `cache()` masivo ni tuning de cluster. |
| **1 SQL Warehouse, 2X-Small** | Power BI en Import; agregados precalculados en gold. |
| **Máx. 5 tareas de job concurrentes** | Orquestar bronze→silver→gold en serie dentro de un job. |
| **1 pipeline activo por tipo** (Lakeflow Declarative Pipelines) | Un único pipeline declarativo; el resto, notebooks/jobs. |
| **Salida a internet restringida a dominios de confianza** (lista no pública, puede variar por región; PyPI sí funciona) | **Riesgo #1 del proyecto:** conectar Structured Streaming a un broker Kafka externo puede estar bloqueado. Se prueba el primer día → ver ADR-002. |
| Sin ubicaciones de almacenamiento personalizadas | Tablas gestionadas + Volumes; no montar S3/ADLS propios. |
| Sin Scala ni R | Todo en Python/SQL. |
| 1 workspace, 1 metastore, sin API de cuenta | Sin entornos dev/prod separados: se simulan con esquemas. |
| Model serving limitado, sin GPU | Scoring en batch sobre tablas gold (suficiente para el dashboard). |
| Apps: máx. 3, apagado a las 24 h | No se usan (opcional). |

### ⚠️ Sobre la verificación con LinkedIn
La documentación oficial solo vincula la verificación con desbloquear **GPU serverless limitado**.
No he encontrado evidencia de que desbloquee salida a internet general. Hazla igualmente (es gratis),
pero **no bases el diseño en esa suposición**: ejecuta la prueba de conectividad de la Fase 5 pronto.

## Kafka — opciones comparadas
| Opción | Coste / duración | Tarjeta | Pros | Contras |
|---|---|---|---|---|
| **Docker local** (Apache Kafka KRaft) | Gratis, ilimitado | No | Controlas todo, aprendes internals (brokers, particiones, consumer groups). | Databricks serverless **no puede** llegar a tu PC. |
| **Aiven for Apache Kafka — Free tier** | Gratis, sin caducidad fija | No | 5 topics × 2 particiones, 250 KiB/s: de sobra para simular eventos. Endpoint público (TLS). | Se apaga por inactividad; sin Kafka Connect; región fija. |
| **Confluent Cloud — trial** | Crédito inicial que caduca a los **30 días** | No para el trial | Ecosistema más usado en ofertas (Schema Registry, Flink, conectores). | Tras 30 días se suspende la org y los recursos se borran si no pagas. Hay que concentrar el trabajo. |

**Recomendación:** Docker local para aprender fundamentos (sin reloj) → Aiven free como broker público
estable → Confluent Cloud solo si quieres Schema Registry, en un *sprint* de 30 días planificado,
activándolo cuando bronze/silver ya funcionen.

## Power BI Desktop
- Gratis y suficiente para el portfolio. Publicar en Power BI Service y compartir requiere licencia Pro
  → se entregan capturas + `.pbix` sin datos sensibles.
- Solo Windows (tu equipo es Windows ✔).
- Conector *Azure Databricks / Databricks* con Server Hostname + HTTP Path del SQL Warehouse y token personal.

## Fuentes
- https://docs.databricks.com/aws/en/getting-started/free-edition-limitations
- https://community.databricks.com/t5/databricks-free-edition-help/whitelist-for-outbound-network-access/td-p/146591
- https://docs.confluent.io/cloud/current/get-started/free-trial.html
- https://aiven.io/docs/products/kafka/free-tier/kafka-free-tier
