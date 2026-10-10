# 01 · Definición del proyecto

## 1. Objetivo
**Técnico (el real):** demostrar un pipeline de datos end-to-end con Kafka, Spark y Databricks, con
decisiones justificadas y documentadas.

**De negocio (el pretexto):** estimar la **probabilidad de impago grave** de una hipoteca a partir de
la información disponible en el momento de su concesión, y clasificar la cartera en bandas de riesgo.

### Definición de trabajo del target (se validará en la Fase 1)
> `default_24m = 1` si el préstamo alcanza **90+ días de retraso** (o termina en ejecución / venta
> corta / REO) en los **primeros 24 meses** desde la originación.

## 2. Tres matices conceptuales que conviene tener claros desde ya

1. **No es un modelo de "conceder o denegar".** Freddie Mac solo contiene préstamos *que ya fueron
   concedidos* (y comprados por Freddie Mac). Nunca vemos a los rechazados, así que el modelo aprende
   "riesgo entre aprobados". En banca esto se conoce como *reject inference* / sesgo de selección.
   Lo formularemos como **"¿a quién hay que vigilar?"** y lo mencionaremos como limitación.
2. **Fuga de información (data leakage).** Las features deben venir *solo* del fichero de originación.
   El fichero de rendimiento mensual sirve para *construir el target*, no como input del modelo
   (salvo en un modelo secundario de "alerta temprana", si lo hacemos).
3. **Ventana de observación.** Para medir 24 meses necesitas cosechas (vintages) con 24 meses de
   historia. Además, 2020-2021 están distorsionadas por las moratorias COVID (forbearance):
   se usarán con cuidado o como test "fuera de distribución".

## 3. Alcance

### Dentro
- Muestra oficial de Freddie Mac (*Sample Dataset*, ~50k préstamos por año), varias cosechas.
- Medallion bronze/silver/gold en Delta Lake (Unity Catalog, Free Edition).
- Reglas de calidad explícitas con métricas registradas en una tabla.
- Simulación streaming: productor Kafka que emite *solicitudes nuevas* (originación) y
  *actualizaciones mensuales* (rendimiento).
- 2–3 modelos comparados (baseline logístico, árbol/boosting), trackeados en MLflow.
- Dashboard Power BI con 3 páginas: cartera, calidad del modelo, calidad de datos.

### Fuera (a propósito)
- Dataset completo de 55M préstamos (no cabe en compute gratuito y no aporta aprendizaje extra).
- Serving del modelo en tiempo real vía endpoint, CI/CD completo, seguridad empresarial.
- Explicabilidad regulatoria, fairness formal (se menciona, no se implementa a fondo).

## 4. Criterios de "hecho" (lo que un reclutador debería ver)
- Repo reproducible con README y diagrama.
- Tablas bronze/silver/gold con esquema documentado.
- Informe de calidad de datos (nulos, rangos, duplicados) antes/después.
- Notebook de ML con métricas justificadas (AUC, PR-AUC, KS, calibración, coste por umbral).
- Capturas del dashboard + vídeo/GIF corto del streaming funcionando.
