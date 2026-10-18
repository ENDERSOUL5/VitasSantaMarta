# ADR-002: Estrategia de Persistencia — Políglota por Dominio frente a Base de Datos Única

| Campo | Valor |
| :--- | :--- |
| **Identificador** | ADR-02 |
| **Estado** | Aprobada · Octubre de 2026 (Fase 2 · Proyecto Capstone) |
| **Formato** | Nygard (Título / Contexto / Decisión / Consecuencias) — Bass, Cap. 22.7 |
| **Trazabilidad ERS v3.0** | RD-02 (persistencia políglota) · RF-05, RF-06 (reservas) · RD-16 (Transactional Outbox) · RNF-01, RNF-02 · REG-02 · Ley 1581/2012 |

> **Nota:** esta decisión es diferente a las listadas como «ejemplos docentes» (SQS, y microservicios frente a monolito en general: esa comparación completa quedó registrada como ADR-01 en el Laboratorio 01).

---

## Contexto y tensión

VITASMARTA maneja cuatro perfiles de datos con requisitos opuestos: (1) catálogo turístico — 80 % de lecturas, ideal para réplicas y caché (RNF-01); (2) reservas — escrituras concurrentes con control optimista, soft lock temporal y ciclo de vida de nueve estados (RF-05, RF-06); (3) sesión y soft lock — volátiles, con TTL de minutos; y (4) eventos de dominio — entrega at-least-once con atomicidad evento-estado (RD-16). Una base única para los cuatro perfiles competiría por recursos entre lecturas masivas y transacciones, sin TTL nativo ni Outbox simple.

Restricciones del Entregable 1: presupuesto USD 20.000, equipo de 5 integrantes, plazo de 6 meses, Ley 1581/2012 y disponibilidad ≥ 99 % (RNF-02).

## Decisión

Adoptar persistencia políglota: una instancia de PostgreSQL independiente por dominio de negocio (Catálogo, Reservas, Usuarios, Prestadores y Reportes); Redis para caché con TTL y soft locks; y la tabla Outbox dentro de la BD de Reservas para garantizar la atomicidad evento-estado sin un coordinador externo (RD-16).

Se descarta la base relacional única (PostgreSQL monolítico) porque el esquema compartido entre Catálogo y Reservas impide escalar el catálogo de forma independiente (RNF-01), los soft locks como filas en la BD principal generarían contención de bloqueos en el pico de 5.000 usuarios (RNF-01) y el aislamiento multi-tenant (REG-02) es más difícil de auditar en un esquema único. También se descarta NoSQL puro (MongoDB): habría exigido reescribir las transacciones ACID de reservas (RF-06-9), riesgo inasumible con el plazo de seis meses y la experiencia del equipo.

## Consecuencias

**Positivas:**

1. Escala independiente por dominio: el Catálogo usa 3 réplicas de lectura sin afectar las transacciones de Reservas.
2. TTL nativo en Redis para caché y soft locks, sin jobs de limpieza.
3. Aislamiento por dominio que facilita la auditoría de la Ley 1581/2012 y la segregación multi-tenant (REG-02).

**Negativas:**

Mayor sobrecosto operativo: administrar cinco instancias de PostgreSQL, Redis (Upstash) y un broker RabbitMQ exige más esfuerzo de DevOps, y la consistencia eventual entre dominios (sin JOIN) exige más pruebas de integración. El piloto lo mitiga con tiers gratuitos (Supabase, Upstash, CloudAMQP), pero en producción real los costos de licenciamiento y mantenimiento se multiplican frente a un monolito.
