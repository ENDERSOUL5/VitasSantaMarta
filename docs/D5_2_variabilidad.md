# D5.2 — Guía de Variabilidad (V1 y V2)

> VITASMARTA · Laboratorio 02 — Documentación Arquitectónica (Bass, Cap. 22.6, p. 346)
> Puntos de variación: lugares donde el sistema puede configurarse o adaptarse
> sin modificar el diseño fundamental.

## Punto de Variación V1 — MOTOR_IA_BACKEND

| Campo | Descripción |
|---|---|
| Identificador | `MOTOR_IA_BACKEND` |
| Ubicación arquitectónica | ServicioIA — RecommendationEngine (descomposición de segundo nivel de M6; punto 1.2). |
| Variantes disponibles | (A) Filtrado colaborativo interno (Python scikit-learn) · (B) Modelo externo vía API (OpenAI/Claude) · (C) Reglas estáticas puras (fallback permanente). |
| Mecanismo de activación | Variable de entorno `IA_BACKEND = collab \| external \| rules` inyectada en la configuración de despliegue del svc-ia (sin reconstruir la imagen); el reinicio afecta únicamente a svc-ia, sin redeploy del API Gateway ni de otros servicios. |
| Restricción | Respetar el contrato `IRecommendation` (punto 1.2) y el objetivo de diseño p95 ≤ 2 s (derivado de RNF-03 y RNF-08-4); incluir el FallbackHandler (RF-07-8). La variante (B) exige documentar la transferencia internacional y sus garantías (REG-01-13, Ley 1581 de 2012); solo se transmiten identificadores seudonimizados y atributos de atractivo, nunca datos personales directos (REG-01-10). |
| Audiencia | Desarrollador principal (Juan Eguis) y Líder Técnico (José Aguilar), roles del ERS §2.7. |

## Punto de Variación V2 — FUENTE_ADAPTADOR_ACTIVO

| Campo | Descripción |
|---|---|
| Identificador | `FUENTE_ADAPTADOR_ACTIVO` |
| Ubicación arquitectónica | M5 ServicioIntegración — capa de adaptadores de la ACL (punto 2.1); punto de extensión previsto por RNF-05-1/RNF-06-2. |
| Variantes disponibles | (A) REST/JSON (piloto actual) · (B) XML/SOAP (grandes prestadores con sistemas legacy) · (C) CSV batch (carga periódica de operadores sin API). |
| Mecanismo de activación | Variable de entorno `ADAPTER_ENABLED = rest,xml,csv` en la configuración de despliegue del svc-integración + registro del adaptador en el catálogo de fuentes (tabla administrativa): la activación de una nueva fuente es operativa (sin tocar código), porque todo adaptador implementa el contrato `ISourceAdapter` (patrón Adapter) registrado por reflexión al arranque. |
| Restricción | El contrato `ISourceAdapter` (normalización a AtractivoTuristico) es invariante (RNF-06-2); el Circuit Breaker (RNF-02-3) y la dead-letter queue aplican a toda variante; el p95 de ingesta debe validarse con la fuente real encendida; la calidad del dato se valida contra el esquema OpenAPI de la fuente antes de publicar la vista materializada (RF-10-4). |
| Audiencia | Integrador Técnico (perfil §6.1) y Diseñador de Datos (Jean Delgado, ERS §2.7). |

> Nota: la alternativa de almacén del catálogo (Elasticsearch/MongoDB) se registra como variante
> futura evaluable en un ADR propio (ver ADR-02, decisión).
