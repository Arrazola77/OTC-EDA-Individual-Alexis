# Reto OT Consulting · Repositorio individual de Alexis Arrazola Jiménez (A01664015)

| Etapa | Contenido | Dónde está |
|---|---|---|
| **Presentación de las Etapas 1 a 4** | Reto, requerimientos, métricas e indicadores, exploración, tratamiento, reglas de negocio, modelo dimensional y acciones futuras | [`Presentacion_Reto_OTC_Etapas_1a4.ipynb`](Presentacion_Reto_OTC_Etapas_1a4.ipynb) |
| 2. Análisis y exploración de datos | Este README y el cuaderno de exploración | [`README.md`](#avance-reto-etapa-2--análisis-y-exploración-de-los-datos-ot-consulting), [`OTC_EDA.ipynb`](OTC_EDA.ipynb), [`Agregar_columnas/`](Agregar_columnas) |
| 3. Tratamiento y acondicionamiento | ETL en Python: métodos M01–M11, bitácora, validaciones y 8 tablas limpias | [`OTC_Etapa3_Acondicionamiento.ipynb`](OTC_Etapa3_Acondicionamiento.ipynb) · [`Etapa3_Acondicionamiento.md`](Etapa3_Acondicionamiento.md) |
| 4. Modelo dimensional lógico | Modelo en estrella `HECHOS_ASIGNACION` con 6 dimensiones | [`Etapa4_Modelo_Dimensional.md`](Etapa4_Modelo_Dimensional.md) · [`modelo_estrella.png`](modelo_estrella.png) |

Los datos de OTC no se suben a este repositorio porque son información del socio formador.

---

# Avance Reto Etapa 2 – Análisis y Exploración de los Datos (OT Consulting)

**Alumno:** Alexis Arrazola Jiménez – A01664015
**Materia:** Soporte analítico para la toma de decisiones
**Socio formador:** OT Consulting (OTC) · **Fecha de corte:** 17-nov-2026

El código de exploración está en OTC_EDA.ipynb (código base del equipo, corre en Colab con los 10 Excel en /content). Este documento con las respuestas, la interpretación y las definiciones es individual.

---

## 1. Objeto de estudio

OTC es una consultora de TI (Oracle) que vive de colocar consultores en proyectos de clientes. Su problema central es que **no tiene control confiable sobre la asignación de su gente**: no sabe con certeza quién está en qué proyecto, desde cuándo, hasta cuándo, con qué rol ni cuánto le cuesta esa asignación. Sin eso no puede medir su banca (gente pagada sin proyecto), anticipar reasignaciones ni costear proyectos.

Lo que muestran los datos:
- De 75 colaboradores activos, solo 30 aparecen en Asignaciones; 45 no tienen ninguna asignación registrada (no se sabe si están en banca o si falta captura).
- Casi todas las asignaciones tienen Rol Asignado = PENDIENTE y la mayoría inicia el 01/01/1990, que es una fecha de relleno.
- Hay proyectos "En Curso" sin personal y otros con fecha fin vencida.

**Hecho a analizar:** la asignación colaborador–proyecto en un periodo, medida por su costo mensual (Sueldo Total Integrado), su duración (fechas) y el perfil de habilidades de la persona.
**Alcance:** las fuentes no traen tarifas ni ingresos, por lo que el estudio mide costo, capacidad y talento, no rentabilidad.

## 2. Metadata de las fuentes (en mis palabras)

| Archivo | Tamaño | Llave | Qué contiene |
|---|---|---|---|
| Catalogo_Colaboradores | 107 × 45 | ID | Ficha de cada persona: datos personales (RFC, CURP, NSS, teléfono, correo y domicilio enmascarados), puesto, departamento, estatus Activo/Baja, fechas de nacimiento, ingreso y baja, desglose de nómina y estatus de cada documento del expediente. |
| Sueldos_Salarios_OTC | 75 × 8 | ID | Nómina de los activos; repite puesto, tipo de nómina, sueldo neto y total integrado del catálogo. |
| Asignaciones_Colaboradores | 31 × 11 | ID Colaborador + Proyecto | Qué persona está en qué cliente/proyecto, rol, tipo de asignación y fechas inicio/fin. |
| Catalogo_Clientes | 12 × 13 | Cód Cliente + Proyecto | Una fila por proyecto: cliente, industria, RFC, contacto, fechas, PM de OTC y estatus. |
| Catalogo_Activos | 123 × 11 | Cód Activo | Equipo de cómputo: marca, modelo, serie, costo, colaborador responsable (por nombre) y fechas. |
| Inversion_Activos_OTC | 123 × 7 | Cód Activo | Duplica código y costo de Activos (coinciden fila a fila); no aporta información nueva. |
| Catalogo_Roles | 55 × 6 | Código | Puestos de OTC con nivel y departamento. |
| Catalogo_Departamentos | 6 × 7 | codigo | Áreas y su objetivo; responsable y centro de costos vacíos. |
| Catalogo_Habilidades | 71 × 6 | código | Habilidades técnicas (H) y blandas (S) por tecnología. |
| Matriz_Habilidades | 54 × 77 | ID Colaborador | Nivel por persona y habilidad: Básico, Intermedio, Avanzado, Experto o vacío. |

Campos clave de costo: Sueldo Neto Total = Neto IMSS + Asimilados/Sindicato + Complementos, y Sueldo Total Integrado = Neto Total + Comisión Nómina + Impuestos IMSS. Verifiqué en el cuaderno que ambas fórmulas cuadran en todos los registros, así que el Total Integrado es el costo mensual real de cada persona para OTC.

## 3. Errores, faltantes e inconsistencias (modelo sugerido)

Modelo sugerido: **bitácora de hallazgos** (una fila por problema) con los campos id, tabla, campo, tipo, descripción, afectados, universo, %, severidad y acción sugerida. El cuaderno la genera completa (sección 8) y la exporta a hallazgos_calidad.csv. Los más importantes:

| Tabla | Campo | Tipo | Problema | Afectados | Severidad | Acción |
|---|---|---|---|---|---|---|
| Asignaciones | Rol Asignado | Faltante | Rol = PENDIENTE | 29 de 31 | Alta | Capturar rol por proyecto |
| Asignaciones | Fecha Inicio | Valor marcador | 01/01/1990 como fecha desconocida | 21 de 31 | Alta | Recuperar fecha real |
| Asignaciones | Fecha Fin | Tipo mezclado | Texto "Indefinido" en campo fecha | varios | Media | Nulo + bandera "indefinida" |
| Asignaciones | Cobertura | Cobertura | Activos sin asignación | 45 de 75 | Alta | Confirmar banca o captura |
| Asignaciones vs Clientes | Fechas | Inconsistencia | Fecha fin distinta entre tablas; proyectos "En Curso" vencidos | varios | Alta | Fuente única de fechas |
| Colaboradores | Puesto | Faltante | Puesto = PENDIENTE en activos | 29 de 75 | Alta | Asignar del catálogo de roles |
| Colaboradores | Fecha de Baja | Inconsistencia | Estatus Baja sin fecha de baja | 18 de 32 | Alta | Recuperar fecha |
| Colaboradores | Sueldo | Inconsistencia | Activos con sueldo 0 / bajas con sueldo > 0 | varios | Alta | Validar con nómina |
| Colaboradores | Tipo Nómina, SGMM, Ciudad | Estandarización | Categorías solapadas y textos libres | varios | Media | Catálogos |
| Clientes | PM OTC | Faltante | Proyecto sin PM | 6 de 12 | Alta | Asignar PM |
| Activos | Costo | Faltante | Costo = 0 | 70 de 123 | Alta | Buscar factura |
| Activos | Colaborador Asignado | Inconsistencia | Equipo a nombre de una baja; liga por nombre, no por ID | 9 | Alta | Recuperar equipo / usar ID |
| Activos | Marca | Estandarización | Misma marca escrita de varias formas | 12 variantes → 5 | Baja | TRIM + MAYÚSCULAS |
| Departamentos | responsable, centroCostos | Columna vacía | 100% vacíos | 6 de 6 | Alta | Definir para costear por área |
| Matriz | Celdas | Faltante | Vacío no distingue "no sabe" de "no evaluado"; activos sin fila | 28 de 75 sin evaluar | Alta | Nivel "Sin conocimiento" + fecha de evaluación |

## 4. Reglas de negocio

1. **RN1** – La llave de un colaborador es su ID; nunca se une por nombre (Activos debe migrar a ID).
2. **RN2** – Un colaborador es activo si su estatus es Activo y no tiene fecha de baja anterior al corte.
3. **RN3** – El costo mensual de una persona es su Sueldo Total Integrado. Un sueldo 0 en un activo se trata como dato faltante, no como costo cero.
4. **RN4** – Un consultor/experto puede tener varias asignaciones, pero la suma de su dedicación no puede exceder 100%.
5. **RN5** – Un gerente o PM puede llevar varios proyectos al mismo tiempo y debe existir en el catálogo de colaboradores.
6. **RN6** – Mientras no exista % de dedicación, el costo de una persona se reparte en partes iguales entre sus asignaciones (2 asignaciones = 50/50).
7. **RN7** – 01/01/1990, Indefinido, Activo y PENDIENTE en campos de fecha o catálogo equivalen a nulo y se marcan con bandera.
8. **RN8** – Las fechas de una asignación deben estar dentro de las fechas del proyecto; la fuente oficial de fechas del proyecto es el Catálogo de Clientes.
9. **RN9** – Un equipo tiene un solo responsable; al dar de baja a alguien se registra la devolución del equipo.
10. **RN10** – Mes laboral = 160 horas (40 h × 4 semanas); costo por hora = Sueldo Total Integrado / 160.
11. **RN11** – Textos sin espacios sobrantes y en mayúsculas; los datos personales sensibles no se usan en el análisis.

## 5. Preguntas detonadoras (solo se definen)

**Asignación y capacidad**
- P1. ¿Cuántos consultores activos están asignados y cuántos están en banca?
- P2. ¿Qué consultores terminan su asignación en los próximos 30, 60 y 90 días?
- P3. ¿Qué proyectos en curso no tienen a nadie asignado?
- P4. ¿Qué consultores tienen más de una asignación y suman más del 100% de dedicación?

**Costo**
- P5. ¿Cuál es el costo por hora de cada consultor, derivado de su salario mensual?
- P6. ¿Cuál es el costo mensual de cada proyecto y de cada cliente?
- P7. ¿Cuánto le cuesta a OTC su banca cada mes?

**Talento, rotación y activos**
- P8. ¿Qué habilidades tienen menos de dos personas en nivel Avanzado o Experto?
- P9. ¿Qué consultores en banca podrían cubrir los proyectos sin personal según sus habilidades?
- P10. ¿Cuál es la rotación mensual y en qué puestos se concentra?
- P11. ¿Cuántos equipos siguen en manos de personas dadas de baja y cuánto valen?

## 6. Métricas

**Existentes en las fuentes:** Sueldo Total Integrado (costo mensual), componentes de nómina, Costo del activo, Nivel de habilidad (1–4), fechas de inicio/fin de asignación y proyecto, fechas de ingreso y baja.

**A agregar (calculadas):**

| Métrica | Cálculo | Pregunta |
|---|---|---|
| Headcount activo | Conteo de activos al corte | P1, P10 |
| Personas en banca | Activos sin asignación vigente | P1, P7 |
| Días para fin de asignación | Fecha fin − fecha de corte | P2 |
| Dedicación total por persona | Suma de % de dedicación | P4 |
| Costo por hora | Sueldo Total Integrado / 160 | P5 |
| Costo mensual asignado | Sueldo Total Integrado / nº de asignaciones (RN6) | P6 |
| Costo de banca | Suma del costo de personas en banca | P7 |
| Personas Avanzado+ por habilidad | Conteo de nivel ≥ 3 | P8, P9 |
| Bajas por mes | Conteo de fechas de baja por mes | P10 |
| Valor de equipos en bajas | Suma del costo de equipos a nombre de bajas | P11 |

## 7. Indicadores (métrica vs meta; metas sugeridas a validar con OTC)

| Indicador | Fórmula | Meta | Pregunta |
|---|---|---|---|
| Tasa de utilización | Activos asignados / activos | ≥ 85% | P1 |
| Asignaciones por vencer | Nº que terminan en 30/60/90 días | 100% con plan de reasignación | P2 |
| Proyectos sin cobertura | Proyectos en curso sin personal / proyectos en curso | 0% | P3 |
| Sobreasignación | Personas con dedicación > 100% | 0 | P4 |
| Costo por proyecto vs presupuesto | Costo mensual asignado del proyecto / presupuesto | ≤ 100% | P6 |
| Peso de la banca | Costo de banca / costo total de nómina | ≤ 10% | P7 |
| Cobertura de habilidad | Personas Avanzado+ por habilidad | ≥ 2 | P8 |
| Rotación mensual | Bajas del mes / headcount | < 2% | P10 |
| Equipos por recuperar | Equipos a nombre de bajas | 0 | P11 |
| Calidad de datos | Hallazgos de severidad Alta abiertos | Tendencia a 0 | Todas |

## 8. Atributos adicionales requeridos

- **% de dedicación** en Asignaciones → repartir el costo real (P4, P6).
- **Facturable (Sí/No)** en Asignaciones → separar trabajo cobrable de interno (P1, P7).
- **Tarifa de venta por hora y presupuesto** en Proyectos → margen y control de costo (P6).
- **Perfil/habilidades requeridas** en Proyectos → cruzar vacantes con la banca (P3, P9).
- **ID del responsable y fecha de devolución** en Activos → ligar equipos por ID (P11).
- **Motivo de baja** en Colaboradores → explicar la rotación (P10).
- **Fecha de evaluación** y nivel "Sin conocimiento" en la Matriz → vigencia del nivel (P8).
- **Responsable y centro de costos** en Departamentos → costeo por área.

## Anexo – Evidencias

El cuaderno OTC_EDA.ipynb de este repositorio:
1. Importa los 10 Excel en Colab y arma un inventario (filas, columnas, nulos, duplicados).
2. Perfila cada tabla columna por columna (tipo, nulos, únicos, ejemplo).
3. Explora colaboradores, nómina (y cuadra fórmulas), expediente, asignaciones, clientes, activos, roles y habilidades, con gráficas.
4. Genera la bitácora de hallazgos y la exporta a hallazgos_calidad.csv.
5. Calcula las primeras métricas: headcount, costo de nómina, costo por hora, cobertura de asignación y costo mensual por cliente/proyecto.
