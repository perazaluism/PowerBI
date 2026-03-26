# 📚 PBIP y TMDL

---

## 🏗️ 1. Estándares de Proyecto (.PBIP)
El formato **Power BI Project (.pbip)** permite la separación de metadatos y el control de versiones, fundamental para entornos de desarrollo colaborativos.

### Estructura de Capas
* **Capa de Informe (`.Report`):** Define la experiencia visual, páginas y recursos estáticos.
* **Capa de Modelo (`.Dataset` o `.SemanticModel`):** Contiene la inteligencia de negocio (tablas, relaciones y lógica).
* **Layout Visual (`diagramLayout.json`):** Controla la disposición de las tablas en la vista de modelo.
    * `location (x, y)`: Coordenadas exactas de posición.
    * `pinKeyFieldsToTop`: Anclaje de llaves en la parte superior de la tabla.
    * `tablesLocked`: Bloqueo de movimiento manual en el lienzo.

---

## 📝 2. Definición de Modelos con TMDL
**TMDL (Tabular Model Definition Language)** es el estándar actual para definir modelos semánticos en texto legible. Su arquitectura se basa en una indentación estricta por niveles (TAB).

### 2.1. Propiedades de los Objetos Medida
Para garantizar la integridad del modelo y una experiencia de usuario profesional, se aplican las siguientes propiedades dentro de los archivos `.tmdl`:

| Propiedad | Descripción Escueta | Ejemplo de Sintaxis |
| :--- | :--- | :--- |
| **measure** | Declaración y DAX (Misma línea). | `measure 'Ventas' = SUM('Sales'[Amount])` |

Las siguientes propiedades necesitan de un nivel de identación más:
| | | |
| :--- | :--- | :--- |
| **formatString** | Máscara de formato estática. | `formatString: #,0.00 €` |
| **formatStringDefinition** | Expresión DAX para formato dinámico. | `formatStringDefinition = [FormatoVar]` |
| **displayFolder** | Carpeta jerárquica (usar `\` para niveles). | `displayFolder: "Finanzas\KPIs"` |
| **description** | Documentación técnica para tooltips. | `description: "Total neto tras impuestos."` |
| **isHidden** | Gestión de visibilidad (medidas técnicas). | `isHidden: true` |
| **lineageTag** | ID único persistente (GUID de trazabilidad). | `lineageTag: a1b2c3d4-e5f6...` |
| **dataCategory** | Categoría de datos (ej. ImageUrl, WebUrl). | `dataCategory: ImageUrl` |
| **annotations** | Metadatos para herramientas externas. | `annotation PBI_Pro = ["Format"]` |

---

## 🚀 3. Grupos de Cálculo (Calculation Groups)
Es la funcionalidad de mayor valor en TMDL para optimizar modelos complejos. Permite aplicar lógica dinámica (ej. Inteligencia de Tiempo) a cualquier medida mediante la función `SELECTEDMEASURE()`.

### 3.1. Ejemplo de Estructura TMDL con Comentarios Técnicos
Un Grupo de Cálculo requiere definir ítems lógicos y columnas de sistema para su funcionamiento:

```tmdl
table 'Inteligencia de Tiempo'
    calculationGroup
        precedence: 1          // Define el orden de ejecución si hay múltiples grupos de cálculo

        calculationItem 'Actual' = SELECTEDMEASURE()

        calculationItem 'YTD' = CALCULATE(SELECTEDMEASURE(), DATESYTD('Calendario'[Fecha]))

    column 'Cálculo'
        dataType: string
        sourceColumn: Name
        sortByColumn: Ordinal  // Vincula el nombre con el orden numérico de visualización

    column Ordinal
        dataType: int64
        isHidden               // Columna oculta que evita el ordenamiento alfabético
        sourceColumn: Ordinal

*Última actualización: Marzo 2026*
