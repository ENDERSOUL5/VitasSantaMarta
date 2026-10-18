# D5.1 — Mapeo entre Vistas: Módulo ↔ Alocación-Despliegue

> VITASMARTA · Laboratorio 02 — Documentación Arquitectónica (Bass, Cap. 22.6, p. 345)
> Par de vistas: Vista de Módulo (2.1) ↔ Vista de Alocación-Despliegue (2.3.a).
> Par distinto del overlay del punto 3 (C&C + Despliegue).
> Relaciones Bass: `allocated-to` (componente → nodo) y `stored-in` (dato → almacén).

| Elemento Vista Módulo | Relación (Bass) | Elemento Vista Alocación | Anotación / Restricción |
|---|---|---|---|
| M1: API Gateway | allocated-to | Render Web Service (×N auto-scale) | Docker; sin estado; escala horizontal (RNF-01). |
| M2: Servicio Catálogo | allocated-to | Render Container (×3 réplicas) | CQRS de solo lectura; sin escritura directa a la BD primaria. |
| M2: Servicio Catálogo | stored-in | PostgreSQL Catálogo (Supabase) | Read Replicas; propiedad de M2; no compartida con M3. |
| M2: Servicio Catálogo | stored-in | Redis (Upstash Free) | TTL = 15 min; caché del catálogo activo. |
| M3: Servicio Reservas | allocated-to | Render Container (×2) | Alta disponibilidad; 2 instancias como mínimo (RNF-02). |
| M3: Servicio Reservas | stored-in | PostgreSQL Reservas (Supabase) | Transaccional; tabla Outbox en la misma BD (RD-16). |
| M3: Servicio Reservas | stored-in | Redis (Upstash Free) | Soft lock TTL = 10 min; espacios de claves separados (`lock:`). |
| M3: Servicio Reservas | stored-in | RabbitMQ (CloudAMQP Free) | Outbox → broker; cola durable; dead-letter habilitada. |
| M4: Servicio Prestadores | allocated-to | Render Container «svc-prestadores» (×1) | Multi-tenant lógico (REG-02); ciclo de vida del contenido (RF-04). |
| M4: Servicio Prestadores | stored-in | PostgreSQL Prestadores (Supabase) | Aislamiento por dominio (ADR-02); auditoría Ley 1581/2012. |
| M5: Servicio Integración | allocated-to | Render Worker «svc-integración» (×1) | Circuit Breaker; vistas materializadas locales (D5). |
| M5: Servicio Integración | stored-in | PostgreSQL Catálogo (Supabase) | Vistas materializadas que alimenta el read model de M2 (RF-10-4); escritura vía lote. |
| M6: Servicio IA | allocated-to | Render Worker (×1, escala independiente) | Pool separado (RNF-01-3); fallback si no está disponible (RF-07-8). |
| M7: Servicio Identidad | allocated-to | Render Container «svc-identidad» (×1) | Emisión y validación JWT; sin estado (tokens firmados). |
| M7: Servicio Identidad | stored-in | PostgreSQL Usuarios (Supabase) | Credenciales y auditoría; cifrado en reposo (RNF-04, Ley 1581/2012). |
| M8: Servicio Ambiental | allocated-to | Render Container «svc-ambiental» (×1) | Monitor de umbrales RF-13; alertas por RabbitMQ (RD-06). |
| M9: Servicio Reportes | allocated-to | Render Container «svc-reportes» (×1) | Consumidor de eventos; exportación PDF/CSV (RF-09-3). |
| M9: Servicio Reportes | stored-in | PostgreSQL Reportes (Supabase) | Agregados pre-calculados; datos derivados de M3 y M8 (RF-09, RF-13). |
| M10: Notificaciones | allocated-to | Render Worker «svc-notificaciones» (×1) | Consumidor async; reintentos con backoff exponencial (RF-12-6). |
| M10: Notificaciones | stored-in | RabbitMQ (CloudAMQP Free) | Consume eventos del bus; misma instancia de broker (relación de consumo). |

**Cobertura:** los diez módulos de primer nivel (M1–M10) tienen al menos una relación `allocated-to` hacia su contenedor; las filas `stored-in` documentan la persistencia políglota (ADR-02: cinco BD PostgreSQL por dominio + Redis + RabbitMQ). Coincide 1:1 con la Figura 2.3a (la contención en un nodo expresa `allocated-to`, Bass, Tabla 22.3).
