## Script para Extracción y Conversión de CPE de PDF a Excel/CSV 📄

Se extrae datos estructurados (Comprobantes de Pago Electrónico - CPE) de archivos PDF, utilizando múltiples estrategias (extracción de tablas y *fallback* basado en expresiones regulares) para unificar y limpiar los resultados en un formato estándar exportable a Excel y CSV.

***

### 📥 Requisitos de Entrada

El script requiere que el usuario cargue uno o varios archivos **PDF** con listados o reportes de CPE, generalmente a través de la función interactiva de carga de archivos de Google Colab (`files.upload()`).

* **Contenido esperado del PDF:** Listados con las siguientes piezas de información en líneas:
    * **Número de CPE** (patrón: `EBxx - xxxx`).
    * **Receptor** (nombre o RUC).
    * **Importe Total** (formato monetario, ej. `S/150.00`).
    * **Fecha de Emisión** (formato: `dd/mm/yyyy`).

***

### 📤 Salida Generada

El script genera un único archivo unificado que contiene la información estandarizada de todos los PDFs procesados.

* **Nombres de archivos de salida:** Utilizan el nombre base del **primer PDF** procesado:
    1.  **CSV:** `/content/{nombre_archivo_base}_listado.csv`
    2.  **XLSX:** `/content/{nombre_archivo_base}_listado.xlsx`
* **Estructura de la tabla de salida (Orden de Columnas):**

| Columna | Descripción | Formato |
| :--- | :--- | :--- |
| **`__archivo__`** | Nombre del PDF original de donde proviene el registro. | `string` |
| **`Nro_CPE`** | Número del comprobante. | `string` |
| **`Receptor`** | Nombre o RUC del receptor. | `string` |
| **`Importe_Total`** | Monto total del comprobante. | `float` (numérico) |
| **`Fecha_Emision`** | Fecha de emisión. | `DD/MM/YYYY` (string) |

***

### 🛠️ Descripción de Librerías

| Librería | Propósito |
| :--- | :--- |
| **`pandas`** | Manejo de datos estructurados (DataFrames), limpieza, unificación y exportación a formatos CSV/Excel. |
| **`pdfplumber`** | Herramienta principal para la apertura de PDFs, extracción de texto y detección de tablas. |
| **`re`** | Módulo para la implementación de expresiones regulares, esencial para el mecanismo de *fallback* de extracción de datos por línea. |
| **`unicodedata`** | Se usa para la limpieza de texto, específicamente para la normalización y eliminación de acentos en los nombres de columnas. |
| **`google.colab.files`** | Permite la subida interactiva de archivos PDF en un entorno de Google Colab. |

***

### 💡 Función Central: `clean_df(df, source_name)`

Esta función es la encargada de la **estandarización y limpieza final** del DataFrame. Su rol es asegurar la integridad y el formato de los datos extraídos antes de la exportación.

* Aplica un **renombrado inteligente** de las columnas extraídas (ej. "Nro Comprobante" se convierte en "Nro\_CPE").
* Utiliza **heurísticas** (patrones de texto o posición) para identificar columnas clave faltantes (`Receptor`, `Importe_Total`, `Fecha_Emision`).
* **Limpia y convierte** `Importe_Total` a un valor numérico (`float`).
* Convierte `Fecha_Emision` a tipo `datetime` para validación y asegura que el formato final de exportación sea **DD/MM/YYYY**.