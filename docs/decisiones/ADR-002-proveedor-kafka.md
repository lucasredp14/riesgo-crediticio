# ADR-002 · Proveedor de Kafka y conexión con Databricks

- **Estado:** propuesta (pendiente de la prueba de conectividad)
- **Fecha:** 2026-09-15

## Contexto
Free Edition restringe la salida a internet a una lista blanca no publicada. No sabemos si Structured
Streaming podrá conectar a un broker Kafka externo.

## Opciones consideradas
1. Aiven Kafka free tier + `spark.readStream.format("kafka")` directo.
2. Confluent Cloud trial (30 días) + lectura directa.
3. Kafka local (Docker) + *bridge* Python que consume y sube micro-lotes JSON a un Volume vía API → Auto Loader.

## Prueba que decide
Desde un notebook serverless: resolver DNS y abrir socket TLS al bootstrap server del broker
(se hará en la Fase 5, pero puede adelantarse en cuanto exista la cuenta).

## Decisión provisional
Docker local para aprender; opción 1 si la prueba pasa; opción 3 si falla.

## Consecuencias
- Con la opción 3 el streaming no es "Databricks lee de Kafka", sino "Kafka → micro-batch → lakehouse".
  Es un patrón real y defendible en entrevista si se explica el porqué.
