# Etapa 4 – Diseño del modelo dimensional lógico (OT Consulting)

**Alumno:** Alexis Arrazola Jiménez – A01664015

## Pasos de Kimball

| Paso | Decisión |
|---|---|
| 1. Proceso de negocio | Asignación de colaboradores a proyectos de clientes |
| 2. Granularidad | Una fila por asignación de un colaborador a un proyecto (`id_asignacion`) |
| 3. Dimensiones | Colaborador, Proyecto (Cliente > Proyecto), Rol, Fecha (4 roles), Estado de la asignación y Calidad de la fecha |
| 4. Hechos | `costo_mensual_asignado`, `pct_dedicacion`, `duracion_dias`, `dias_para_fin`, `n_asignaciones_persona`, `costo_mensual_persona`, `costo_hora_persona` |

La tabla de origen es `asignaciones_acondicionada` de la Etapa 3 (31 filas y 34 columnas). Cada uno de sus 34 atributos queda en un solo lugar del modelo.

## Diagrama

![Modelo en estrella](modelo_estrella.png)

```mermaid
erDiagram
    DIM_COLABORADOR ||--o{ HECHOS_ASIGNACION : id_colaborador
    DIM_PROYECTO ||--o{ HECHOS_ASIGNACION : id_proyecto
    DIM_ROL ||--o{ HECHOS_ASIGNACION : id_rol
    DIM_FECHA ||--o{ HECHOS_ASIGNACION : "4 roles de fecha"
    DIM_ESTADO_ASIGNACION ||--o{ HECHOS_ASIGNACION : id_estado_asignacion
    DIM_CALIDAD_FECHA ||--o{ HECHOS_ASIGNACION : id_calidad_fecha

    HECHOS_ASIGNACION {
        string id_asignacion "dimension degenerada"
        string id_colaborador FK
        string id_proyecto FK
        int id_rol FK
        int id_estado_asignacion FK
        int id_calidad_fecha FK
        int fecha_inicio_efectiva FK
        int fecha_fin_efectiva FK
        int fecha_inicio_registrada FK
        int fecha_fin_registrada FK
        float costo_mensual_asignado "MXN, aditiva"
        float pct_dedicacion "0-1, semiaditiva"
        float duracion_dias "aditiva"
        float dias_para_fin "no aditiva"
        int n_asignaciones_persona "no aditiva"
        float costo_mensual_persona "no aditiva"
        float costo_hora_persona "no aditiva"
    }
    DIM_COLABORADOR {
        string id_colaborador PK
        string nombre_colaborador
        string familia_puesto
        string esquema_nomina
    }
    DIM_PROYECTO {
        string id_proyecto PK
        string cliente
        string proyecto
        bool proyecto_comodin
    }
    DIM_ROL {
        int id_rol PK
        string rol_interno
        string rol_asignado
        string rol_efectivo
        string fuente_rol
        bool rol_faltante
    }
    DIM_FECHA {
        int id_fecha PK
        date fecha
        int anio
        int trimestre
        int mes
        string nombre_mes
        int dia
    }
    DIM_ESTADO_ASIGNACION {
        int id_estado_asignacion PK
        string tipo_asignacion
        string estatus_asignacion
        string estado_vigencia
        bool persona_libre_al_vencer
    }
    DIM_CALIDAD_FECHA {
        int id_calidad_fecha PK
        string fuente_fecha_inicio
        string fuente_fecha_fin
        bool fecha_inicio_desconocida
        bool fin_indefinido
        bool ajustada_r08
    }
```

## Decisiones de diseño
- **Estrella, no copo de nieve:** Cliente y Proyecto quedan juntos en una dimensión para que las consultas usen menos uniones.
- **Dimensión degenerada:** `id_asignacion` se queda en la tabla de hechos.
- **Fecha con rol múltiple (*role-playing*):** `DIM_FECHA` se usa para inicio y fin, tanto efectivos como registrados. Tiene un miembro "desconocido" (`id_fecha = 0`) para las fechas que no existen en el origen.
- **Dimensiones de banderas (*junk*):** `DIM_ESTADO_ASIGNACION` y `DIM_CALIDAD_FECHA` agrupan banderas de baja cardinalidad, cada una con su llave sustituta.

La construcción y la prueba del modelo en pandas están en la sección 7 de [`Presentacion_Reto_OTC_Etapas_1a4.ipynb`](Presentacion_Reto_OTC_Etapas_1a4.ipynb). Ahí se verifica que hay 0 llaves huérfanas y que la métrica aditiva cuadra con la Etapa 3.
