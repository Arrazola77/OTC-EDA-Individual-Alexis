# Reto OTC · Columnas calculadas del catálogo de colaboradores

**Alexis Arrazola Jimenez** · A01664015
Soporte analítico para la toma de decisiones · Tecnológico de Monterrey

## Contenido

| Archivo | Descripción |
|---|---|
| `Agregar_columnas_A01664015.ipynb` | Notebook que agrega al catálogo de colaboradores del ERP de OT Consulting columnas de costo (por hora, diario, anual, interno e indirecto), edad, antigüedad y alertas de calidad de datos; elimina datos personales y cruza el catálogo con las asignaciones para obtener el costo mensual por cliente y proyecto. |

## Cómo correrlo

1. Abrir el notebook en Google Colab.
2. Ejecutar todas las celdas (*Entorno de ejecución → Ejecutar todas*).
3. Cuando lo pida, subir `Catalogo_Colaboradores.xlsx` y `Asignaciones_Colaboradores.xlsx`.
4. Se descargan `Colaboradores_actualizado.xlsx` y `Costo_por_proyecto.xlsx`.

Los archivos de datos de OTC no se suben a este repositorio porque son información del socio formador.

## Supuestos

- 160 horas laborales al mes (8 h × 20 días hábiles).
- *Sueldo Total Integrado* es el costo mensual de cada colaborador para OTC.
- Los promedios solo consideran colaboradores activos con sueldo mayor a 0.
- Si un colaborador está en más de un proyecto, su costo se reparte en partes iguales.

## Resultados principales

- Nómina mensual activa: $3.92 millones; costo promedio de ~$350 por hora.
- Metalsa "Asignación indefinida": ~$1.27 millones al mes, el proyecto con mayor costo.
- Solo 30 de 75 activos (40 %) tienen asignación registrada.
- Alertas: 5 activos con sueldo 0, 12 bajas con sueldo, 18 bajas sin fecha y 29 activos con puesto PENDIENTE.
