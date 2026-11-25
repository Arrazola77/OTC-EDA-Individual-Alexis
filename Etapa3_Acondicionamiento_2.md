# Etapa 3 – Tratamiento y acondicionamiento de datos (OT Consulting)

**Alumno:** Alexis Arrazola Jiménez – A01664015 · **Fecha de corte:** 17-nov-2026

El cuaderno [`OTC_Etapa3_Acondicionamiento.ipynb`](OTC_Etapa3_Acondicionamiento.ipynb) es el ETL del reto. El código es el del equipo y yo lo uso en mi repositorio individual. Toma los 10 Excel de OTC y hace lo siguiente:

1. Recuerda los 60 hallazgos de calidad de la Etapa 2 (21 de severidad alta).
2. Aplica los métodos de corrección **M01 a M11**, que se derivan de las reglas de negocio **R01 a R12**.
3. Agrega los atributos que hacen falta para responder las preguntas **P1 a P11**, por ejemplo `costo_hora`, `en_banca`, `dias_para_fin` y `costo_mensual_asignado`.
4. Corre **25 validaciones automáticas** (24 aprobadas) y registra **37 correcciones** en una bitácora.
5. Exporta **8 tablas acondicionadas** y su metadata (176 columnas documentadas).

**Principio de trabajo:** nunca se inventa un dato. Lo que no se puede recuperar queda en nulo con una bandera que explica por qué.

| Sección del cuaderno | Contenido |
|---|---|
| 0 | Configuración y carga de los 10 Excel |
| 1 | Recordatorio de las inconsistencias de la Etapa 2 |
| 2–6 | Colaboradores, proyectos, asignaciones, activos y matriz de habilidades |
| 7–8 | Integración: banca, equipos, habilidades e indicadores por proyecto |
| 9 | Bitácora de correcciones y validaciones; se detiene si falla una validación crítica |
| 10–12 | Tablas finales, respuestas a P1–P11 y metadata |
| 13–14 | Gráficas y exportación a CSV y Excel |

**Datos:** los Excel de OTC y las tablas de salida **no se suben** a este repositorio porque son información del socio formador. Para correr el cuaderno en Colab, súbelos a `/content`.
