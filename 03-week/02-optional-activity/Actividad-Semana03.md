# Semana 3 · Bases de datos transaccionales (OLTP) vs. analíticas (OLAP)
**Inteligencia de Negocios — Ingeniería de Sistemas — CORHUILA**

## 1. Clasificación de dos sistemas reales - Caso Universidad Corhuila

### Sistema A — Sistema de Matrícula 
**Clasificación: OLTP** (On-Line Transaction Processing).
**Justificación:** Se clasifica como OLTP porque su función principal es registrar transacciones puntuales y frecuentes inscribir a un estudiante, cambiar una materia, procesar un retiro donde cada operación es corta y afecta pocos registros a la vez. Su diseño prioriza la consistencia y velocidad en el momento de la transacción (por ejemplo, evitar que se dupliquen cupos o que un estudiante quede inscrito dos veces en la misma materia), y está optimizado para escritura constante (insertar, actualizar, eliminar), no para análisis histórico o masivo.

### Sistema B — Sistema de Facturación
**Clasificación: OLTP** (On-Line Transaction Processing).
**Justificación:** También se clasifica como OLTP porque cada factura, pago o nota crédito es una transacción individual que exige integridad crítica (no puede fallar a mitad de camino) y alta concurrencia (muchos estudiantes pagando al mismo tiempo sin que se corrompan los registros). Su función es procesar las operaciones financieras del día a día emitir, cobrar, anular, no responder preguntas analíticas de largo plazo.

## 2. Pregunta de análisis

## Pregunta
 
> **¿Cuál ha sido la tendencia de matriculados por programa académico en los últimos 5 años, y qué porcentaje de crecimiento o decrecimiento tuvo cada programa semestre a semestre?**
 
- **Histórica** → abarca 5 años de datos, mientras que el sistema operativo (OLTP) está pensado para consultar y modificar información reciente o vigente, no para recorrer históricos extensos.
- **Agregada** → requiere agrupar y comparar por programa y por semestre, justo el tipo de cálculo para el que el modelo normalizado de un OLTP no está optimizado.

## 3. Por qué llevar esos datos a un data warehouse mejora la respuesta

- **Rendimiento:** 	La base de matrícula está optimizada para transacciones rápidas de un solo registro, no para escanear millones de filas históricas. Consultar 5 años de datos ahí generaría lentitud y podría afectar a los estudiantes que se están matriculando en ese momento.
- **Estructura:** El warehouse usa modelos como esquema estrella (hechos de matrícula + dimensiones de tiempo, programa, sede), diseñados específicamente para agregaciones y comparaciones, algo que el modelo normalizado del OLTP no facilita.
- **Histórico:** Los sistemas OLTP suelen conservar solo los datos "vigentes" o recientes. El warehouse está pensado para acumular histórico completo sin afectar el rendimiento del sistema operativo.
  
## Diagrama de flujo
 
```
┌───────────────────────┐
│  Sistema de Matrícula  │
│    (Base OLTP)         │
│                        │
│  Transacciones diarias │
│  - Inscripciones       │
│  - Cambios de materia  │
│  - Retiros             │
└───────────┬────────────┘
            │
            │  Carga periódica
            ▼
┌───────────────────────┐
│         ETL            │
│                        │
│  - Extrae datos crudos │
│  - Limpia y corrige    │
│  - Transforma estructura│
└───────────┬────────────┘
            │
            ▼
┌───────────────────────┐
│    Data Warehouse       │
│                        │
│  - Modelo estrella      │
│  - Histórico completo   │
│  - Optimizado para      │
│    consultas agregadas  │
└───────────┬────────────┘
            │
            │  Consulta analítica
            ▼
┌───────────────────────┐
│   Reporte Gerencial     │
│                        │
│  Ej: Tendencia de       │
│  matriculados por       │
│  programa (5 años)      │
└───────────────────────┘
```

El flujo funciona así: el sistema de matrícula genera transacciones diarias (inscripciones, retiros), el ETL las extrae y limpia periódicamente (cada noche o cada semana), el warehouse las acumula en un modelo histórico optimizado, y desde ahí la rectoría consulta reportes de tendencias sin tocar la base operativa.

---
