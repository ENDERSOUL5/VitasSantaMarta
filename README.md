# VitasSantaMarta

## Índice de Artefactos y Trazabilidad

El siguiente índice mapea cada artefacto del repositorio con su respectivo punto del laboratorio y el requisito del Entregable 1 asociado. Los diagramas se encuentran en formato fuente PlantUML para su regeneración, y la especificación ACME cuenta con su debida evidencia de validación sintáctica en la carpeta `docs/`.

| PUNTO | ARTEFACTO / VISTA | ARCHIVO EN EL REPOSITORIO | TRAZABILIDAD ERS v3.0 |
| :---: | :--- | :--- | :--- |
| **1.1** | Notación informal (Motor IA) | `diagrams/D1_1_informal.puml` | RF-07, RF-07-8 |
| **1.2** | Notación semiformal UML | `diagrams/D1_2_semiformal.puml` | RF-07, RF-07-3 |
| **1.3** | Notación formal ACME | `diagrams/D1_3_formal.acme` | RF-07, RNF-03 |
| **2.1** | Vista de Módulo | `diagrams/D2_1_modulo.puml` | RD-01, RD-16 |
| **2.2** | Vista C&C (alta demanda) | `diagrams/D2_2_cc.puml` | RNF-01, RNF-03 |
| **2.3a** | Vista de Despliegue | `diagrams/D2_3a_alocacion.puml` | Nodos cloud reales · stored-in · «migrates-to» (RNF-02) |
| **2.3b** | Asignación de trabajo | `diagrams/D2_3b_trabajo.puml` | ERS v3.0, sección 2.7 |
| **2.4a** | Calidad: Disponibilidad | `diagrams/D2_4a_disponibilidad.puml` | RNF-02 |
| **2.4b** | Calidad: Escalabilidad | `diagrams/D2_4b_escalabilidad.puml` | RNF-01 |
| **3** | Overlay C&C + Despliegue | `diagrams/D3_overlay.puml` | RNF-01, RNF-02 |
| **4.1** | Secuencia (recomendación) | `diagrams/D4_1_secuencia.puml` | RF-07, RNF-03 |
| **4.2** | Máquina de estados (IA) | `diagrams/D4_2_estados.puml` | RF-07-8, RF-07-3 |
| **5.3** | ADR-02 (persistencia) | `docs/ADR-02_Persistencia.md` | RD-02, RF-05, RF-06 |
