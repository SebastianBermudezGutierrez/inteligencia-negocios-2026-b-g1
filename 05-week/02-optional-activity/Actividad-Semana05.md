# Resumen Corte 1 y Caso Práctico — Preparación para el parcial

## 1. Resumen de conceptos del Corte 1

### Cadena del dato: Dato → Información → Conocimiento
El **dato** es un valor crudo, sin procesar ni contexto (ej. un ID, una fecha, un número de venta). La **información** es el dato organizado y contextualizado que responde una pregunta concreta (ej. "se vendieron 200 unidades del producto X en marzo"). El **conocimiento** es la interpretación de por qué ocurre algo, que permite tomar decisiones (ej. "las ventas de X suben en marzo porque coincide con temporada de gripa").

### KPI (Indicador clave de desempeño)
Un KPI mide el desempeño de un proceso frente a una meta. Se define por: **qué mide** (el fenómeno de negocio), su **fórmula** (cómo se calcula, generalmente una razón o porcentaje) y su **meta** (el umbral deseado, ej. "tasa de deserción ≤ 5%"). Un buen KPI siempre debe habilitar una **decisión** concreta, no solo describir un número.

### OLTP vs OLAP
El **OLTP** procesa transacciones diarias (inscribir, facturar, vender), con un diseño normalizado y optimizado para escritura rápida, y trabaja principalmente con datos recientes o vigentes. Ejemplos: sistema de matrícula, facturación.

El **OLAP** analiza datos históricos y agregados, con un diseño desnormalizado (estrella o copo de nieve) optimizado para lectura y agregación, y trabaja con volúmenes históricos multianuales. Ejemplos: reporte gerencial, dashboard de ventas.

### Data Warehouse
Es un repositorio centralizado que consolida datos de uno o varios sistemas OLTP, transformados y organizados para el análisis. Se alimenta mediante procesos **ETL** (Extracción, Transformación, Carga) y su valor principal está en conservar el **histórico completo** sin afectar el sistema operativo, optimizar el **rendimiento** de consultas agregadas, y dar una **estructura** adecuada para el análisis (modelo dimensional).

### Modelo Estrella
Es la forma más común de organizar un data warehouse. La **tabla de hechos** es el centro del modelo: representa un evento de negocio medible (ej. una venta, una matrícula) y contiene las **medidas** (valores numéricos que se suman: cantidad, valor, etc.) y las **claves foráneas** hacia las dimensiones. Las **dimensiones** son tablas que describen el contexto del hecho (quién, qué, cuándo, dónde); siempre debe existir una **dimensión Tiempo**. Se llama "estrella" porque la tabla de hechos queda en el centro conectada directamente a cada dimensión, sin niveles intermedios.

---

## 2. Caso práctico: Cadena de farmacias

**Contexto:** La cadena quiere analizar sus ventas por producto, sucursal y mes.

### Tabla de hechos: `Hechos_Ventas`

**Qué evento representa:** cada fila es **una venta de un producto en una sucursal, en una fecha específica** (el nivel de detalle más granular disponible: el ticket o línea de venta).

**Medidas (se suman):**

| Medida | Descripción |
|---|---|
| `cantidad_vendida` | Unidades del producto vendidas en esa transacción |
| `valor_venta` | Monto total de la venta (precio × cantidad) |
| `costo_total` | Costo del producto vendido (para calcular margen) |
| `unidades_devueltas` | Unidades devueltas, si aplica |

### Dimensiones

**Dim_Tiempo**
| Atributo | Ejemplo |
|---|---|
| `id_tiempo` (PK) | 20260301 |
| `fecha` | 2026-03-01 |
| `mes` | Marzo |
| `trimestre` | Q1 |
| `año` | 2026 |

**Dim_Producto**
| Atributo | Ejemplo |
|---|---|
| `id_producto` (PK) | PR0456 |
| `nombre_producto` | Acetaminofén 500mg |
| `categoria` | Analgésicos |
| `laboratorio` | Genfar |
| `requiere_receta` | No |

**Dim_Sucursal**
| Atributo | Ejemplo |
|---|---|
| `id_sucursal` (PK) | S012 |
| `nombre_sucursal` | Farmacia Centro |
| `ciudad` | Neiva |
| `zona` | Urbana |
| `tipo_sucursal` | Propia / Franquicia |

### Justificación del diseño

La tabla de hechos se centra en la venta porque es el evento medible y repetible que la cadena quiere analizar, y sus medidas son aditivas, es decir, se pueden sumar sin perder sentido, como el total de unidades o el total en pesos. La dimensión Tiempo es obligatoria porque la pregunta de negocio pide análisis "por mes", y además permite comparar tendencias entre periodos, como crecimiento o estacionalidad. La dimensión Producto permite responder qué productos son los más vendidos o más rentables, y agrupar por categoría o laboratorio. La dimensión Sucursal permite comparar el desempeño entre puntos de venta e identificar sucursales con bajo o alto rendimiento. Finalmente, este diseño en estrella facilita consultas como "ventas totales por sucursal y mes" o "producto más vendido por categoría" con joins simples y buen rendimiento, algo que sería lento y complicado de calcular directamente sobre la base operativa (OLTP) del punto de venta.

---

## 3. Autoevaluación honesta antes del parcial

| Tema | Lo domino | Lo entiendo pero con dudas | Necesito repasarlo |
|---|---|---|---|
| Cadena del dato (Dato → Información → Conocimiento) | ☐ | ☑ | ☐ |
| Definición y estructura de un KPI (qué mide, fórmula, meta) | ☐ | ☐ | ☑ |
| Diferencias OLTP vs OLAP | ☐ | ☑ | ☐ |
| Qué es y para qué sirve un Data Warehouse | ☑ | ☐ | ☐ |
| Proceso ETL | ☑ | ☐ | ☐ |
| Modelo estrella: tabla de hechos y medidas | ☐ | ☐ | ☑ |
| Modelo estrella: dimensiones y atributos | ☐ | ☐ | ☑ |
| Diseñar un modelo estrella desde un caso nuevo | ☐ | ☐ | ☑ |
| Tipos de analítica (descriptiva, diagnóstica, predictiva, prescriptiva) | ☐ | ☑ | ☐ |
