# Semana 4 · El modelo estrella — hechos y dimensiones
**Inteligencia de Negocios — Ingeniería de Sistemas — CORHUILA**

# Modelo dimensional (esquema estrella) — Proceso de negocio: Matrícula

## 1. Tabla de hechos: `Hechos_Matricula`

**Qué evento representa:** Cada fila representa **una matrícula de un estudiante en una materia, en un semestre específico**. Es el evento de negocio más granular del proceso de matrícula.

**Medidas (los valores que se suman/agregan):**

| Medida | Descripción |
|---|---|
| `cantidad_matriculados` | 1 por cada registro (permite contar matrículas al sumar) |
| `valor_matricula` | Valor cobrado por esa matrícula (en pesos) |
| `creditos_matriculados` | Número de créditos académicos de la materia matriculada |
| `cantidad_retiros` | 1 si el estudiante se retiró de esa materia, 0 si no (permite calcular tasas de deserción) |

---

## 2. Dimensiones

### Dimensión Tiempo (`Dim_Tiempo`)
| Atributo | Ejemplo |
|---|---|
| `id_tiempo` (PK) | 20261 |
| `semestre` | 2026-1 |
| `año` | 2026 |
| `periodo_academico` | Primer semestre |
| `fecha_inicio` | 2026-01-15 |
| `fecha_fin` | 2026-06-15 |

### Dimensión Estudiante (`Dim_Estudiante`)
| Atributo | Ejemplo |
|---|---|
| `id_estudiante` (PK) | E00234 |
| `nombre` | Ana Martínez |
| `genero` | Femenino |
| `estrato` | 3 |
| `ciudad_origen` | Neiva |
| `tipo_admision` | Regular / Transferencia |

### Dimensión Programa Académico (`Dim_Programa`)
| Atributo | Ejemplo |
|---|---|
| `id_programa` (PK) | P010 |
| `nombre_programa` | Ingeniería de Sistemas |
| `facultad` | Ingeniería |
| `nivel` | Pregrado |
| `modalidad` | Presencial |

### Dimensión Materia (`Dim_Materia`)
| Atributo | Ejemplo |
|---|---|
| `id_materia` (PK) | M0456 |
| `nombre_materia` | Cálculo II |
| `area_academica` | Ciencias Básicas |
| `nivel_dificultad` | Alto |

---

## 3. Diagrama de la estrella

```
                    ┌─────────────────┐
                    │   Dim_Tiempo     │
                    │ ─────────────── │
                    │ id_tiempo (PK)   │
                    │ semestre         │
                    │ año              │
                    │ periodo_academico│
                    └────────┬─────────┘
                             │
┌──────────────────┐        │        ┌──────────────────┐
│  Dim_Estudiante   │        │        │   Dim_Programa    │
│ ─────────────── │        │        │ ─────────────── │
│ id_estudiante(PK)│        │        │ id_programa (PK) │
│ nombre            │        │        │ nombre_programa   │
│ genero            │───┐    │    ┌───│ facultad          │
│ estrato           │   │    │    │   │ nivel             │
│ ciudad_origen     │   │    │    │   │ modalidad         │
└──────────────────┘   │    │    │   └──────────────────┘
                        │    │    │
                        ▼    ▼    ▼
                 ┌─────────────────────────┐
                 │    Hechos_Matricula      │
                 │ ─────────────────────── │
                 │ id_tiempo (FK)           │
                 │ id_estudiante (FK)       │
                 │ id_programa (FK)         │
                 │ id_materia (FK)          │
                 │ cantidad_matriculados    │
                 │ valor_matricula          │
                 │ creditos_matriculados    │
                 │ cantidad_retiros         │
                 └────────────┬─────────────┘
                              │
                    ┌─────────┴─────────┐
                    │    Dim_Materia     │
                    │ ─────────────────  │
                    │ id_materia (PK)    │
                    │ nombre_materia     │
                    │ area_academica     │
                    │ nivel_dificultad   │
                    └────────────────────┘
```

---

## 4. Preguntas de negocio que responde el modelo

### Pregunta 1: ¿Cuántos estudiantes se matricularon por programa académico en cada semestre de los últimos 5 años?

**Cómo se responde:** se filtra `Dim_Tiempo` por el rango de semestres deseado, se agrupa por `Dim_Programa.nombre_programa` y `Dim_Tiempo.semestre`, y se suma la medida `cantidad_matriculados` de la tabla de hechos. El resultado permite ver la tendencia de crecimiento o decrecimiento de cada programa a lo largo del tiempo.

### Pregunta 2: ¿Cuál es la tasa de retiro por materia y qué estrato socioeconómico presenta más retiros?

**Cómo se responde:** se agrupa la tabla de hechos por `Dim_Materia.nombre_materia` y `Dim_Estudiante.estrato`, sumando `cantidad_retiros` sobre `cantidad_matriculados` para calcular el porcentaje. Esto ayuda a identificar si hay materias críticas o grupos poblacionales con mayor riesgo de deserción, información clave para diseñar programas de apoyo focalizados.
