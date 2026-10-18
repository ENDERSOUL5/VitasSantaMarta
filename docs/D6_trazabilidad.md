# D6 — Trazabilidad Stakeholder → Artefactos

> VITASMARTA · Laboratorio 02 — Documentación Arquitectónica (Bass, Cap. 22.8, pp. 347-350)
> Fuente: ERS v3.0, sección 2.4 (8 stakeholders base) + 2 perfiles operativos desglosados.

| Stakeholder (ERS v3.0) | Rol / Interés | Artefactos útiles | Información específica que busca |
|---|---|---|---|
| Turistas y visitantes (nac./int.) | Usuario final · buscar y reservar experiencias turísticas | 2.2 C&C · 4.1 Secuencia | Artefactos de uso interno del equipo: garantizan que la solicitud del turista llegue en pocos pasos, con respuesta explicada (RF-07-3) y tiempos prometidos; la información hacia el turista se comunica en lenguaje llano, no mediante las vistas. |
| Pequeños y medianos operadores (PYMES) | Usuario / proveedor de datos · gestionar oferta y cupos | 2.1 Módulo · 2.3b Asignación · 5.1 Mapeo | M4-Prestadores es un servicio propio e independiente (REG-02); 2.3b y 5.1 muestran dónde corre M4 (svc-prestadores), en qué BD persiste y quién lo implementa. |
| Grandes prestadores (hoteles, agencias) | Proveedor de datos / integración API externa | 2.1 Módulo · 2.4a Calidad-Disponibilidad (CB) · 5.2 V2 | La ACL de M5 con Circuit Breaker (2.1); cómo el CB protege la operación ante fuentes inestables (RNF-02-3); la guía V2 (FUENTE_ADAPTADOR_ACTIVO) explica cómo incorporar un adaptador XML/SOAP sin rediseño. |
| Entidades de gestión turística (Alcaldía, ProColombia, FONTUR) | Patrocinador · indicadores de demanda y sostenibilidad | 2.4a Disponibilidad · 2.4b Escalabilidad · 5.1 Mapeo | Estrategias diseñadas para soportar SLA ≥ 99 % (RNF-02) y picos turísticos (RNF-01); el mapeo muestra la infraestructura real de los reportes de demanda (M9: svc-reportes + BD Reportes). |
| Autoridades ambientales (CORPAMAG, PNN Tayrona) | Regulador · control de capacidad de carga | 2.2 C&C · 4.1 Secuencia · 4.2 Estados · 5.3 ADR-02 | svc-ambiental [RF-13] en la C&C (2.2); consulta capacity-status con umbrales 80/85/90 (RF-13-11); ruta RECHAZADA_POR_CAPACIDAD con sugerencia de alternativas (RF-07-4, RF-13-12); aislamiento de persistencia por dominio (ADR-02). |
| Administradores del sistema | Operador interno · configuración, monitoreo y control | 2.3a Despliegue · 2.4a Disponibilidad · 5.2 Variabilidad · 5.3 ADR-02 | Nodos cloud y auto-escalado; cómo cambiar la BD o el backend de IA sin redeploy global; por qué existen cinco BD separadas, una por dominio. |
| Equipo de desarrollo (5 estudiantes) | Desarrollador · implementar la arquitectura en 6 meses | Todos (puntos 1–5), en especial 2.1, 2.3b, 5.1 | La tabla de responsabilidades indica qué construir; 2.3b define quién implementa qué; 5.1 muestra el despliegue que deben configurar. |
| Docente evaluador (ABET SO2) | Evaluador · verificar calidad arquitectónica y trazabilidad | Todos + ADR + Reflexiones del punto 1 | Coherencia ERS ↔ vistas; reflexiones concretas (no genéricas); rationale documentado por decisión (ADR-01/ADR-02). |
| Analista Turístico (ProColombia) — perfil | Consumidor de indicadores y reportes de demanda | 2.1 Módulo · 2.3a Despliegue · 2.4b Escalabilidad | M9 desacoplado (2.1); svc-reportes consume eventos del bus sin tocar el camino transaccional (2.3a); dashboards disponibles en pico (2.4b). |
| Integrador Técnico — perfil | Configurar y monitorear adaptadores de fuentes externas | 2.3a Despliegue · 2.4a Disponibilidad · 5.2 V2 · 5.3 ADR-02 | Contenedor del worker de integración (2.3a); Circuit Breaker de los adaptadores (RNF-02-3); cómo añadir una fuente nueva por configuración (`ADAPTER_ENABLED`) sin rediseño. |

## Análisis de cobertura (6.2)

- **¿Hay algún stakeholder sin artefacto útil?** Los ocho stakeholders del ERS v3.0 tienen al menos dos
  artefactos útiles. La cobertura más débil es la de Turistas y visitantes: las vistas describen el sistema
  desde quien lo construye, no de quien lo usa. Artefactos complementarios ya planificados: (1) Diagrama de
  Contexto (Fase 3) y (2) wireframes de flujos críticos (entregable del Documento de Diseño del Capstone).
- **¿Hay algún artefacto sin stakeholder identificado?** No: todos los artefactos de los puntos 1–5 tienen
  audiencia. La especificación ACME (1.3) tiene la audiencia más acotada (Docente Evaluador y Líder Técnico
  como validación formal); se mantiene por exigencia académica y porque reveló la inconsistencia del
  FallbackHandler documentada en la Reflexión 1.3.

> Equivalencias de perfiles: «Arquitecto» → Líder Técnico (José Aguilar, ERS §2.7); «Analista de Calidad» →
> Responsable de Validación y Calidad (Mauricio Otero, ERS §2.7); «equipo de infraestructura» →
> Administradores del sistema (ERS §2.4).
