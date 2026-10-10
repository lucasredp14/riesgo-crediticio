# ADR-001 · Lakehouse Delta con arquitectura medallion

- **Estado:** aceptada
- **Fecha:** 2026-09-15

## Contexto
Hace falta almacenamiento cloud consultable por SQL/Power BI y reprocesable, dentro de Databricks Free Edition
(sin almacenamiento externo propio).

## Opciones consideradas
1. Tablas Delta gestionadas en Unity Catalog, capas bronze/silver/gold.
2. Base de datos relacional externa (PostgreSQL gestionado).
3. Ficheros Parquet en Volumes.

## Decisión
Opción 1.

## Consecuencias
- ✔ ACID, MERGE, time travel, esquema forzado; SQL Warehouse como capa de servicio.
- ✔ Reproceso desde bronze sin volver a descargar.
- ✖ Dependencia de Databricks; los datos se triplican (aceptable con la muestra).
- 👁 Vigilar tamaño de tablas por los límites de compute.
