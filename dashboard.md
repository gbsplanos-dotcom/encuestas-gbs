# 📡 Dashboard — Control de Transmisiones

> El Dashboard es el centro de control del sistema GBS Encuestas. Consolida toda la información de las encuestas de campo y genera los reportes de cumplimiento.

---

## Índice

1. [¿Qué es y para qué sirve?](#1-qué-es-y-para-qué-sirve)
2. [Cómo acceder](#2-cómo-acceder)
3. [Pestañas del Dashboard](#3-pestañas-del-dashboard)
4. [Cómo funciona generarDashboard](#4-cómo-funciona-generardashboard)
5. [Trigger horario](#5-trigger-horario)
6. [Mantenimiento mensual](#6-mantenimiento-mensual)
7. [Ruteros mensuales](#7-ruteros-mensuales)
8. [Cómo interpretar el cumplimiento](#8-cómo-interpretar-el-cumplimiento)

---

## 1. ¿Qué es y para qué sirve?

El Dashboard de Control de Transmisiones es un Google Sheet que se actualiza automáticamente cada hora. Permite saber:

- ✅ Qué evaluadores están cumpliendo con sus encuestas
- ❌ Qué PDVs no han sido visitados
- 📅 Qué encuestas llegaron fuera de fecha
- 📊 Resumen semanal y mensual por evaluador

**Sheet:** `1IvaFUx_Ne7e0GB6EwMa5675TfxX2mp3wWJI8ReGy45U`

---

## 2. Cómo acceder

**Al Dashboard (lectura):**
```
Abrir el link del Sheet directamente
```

**Al script (para ejecutar funciones):**
```
1. Abrir el Sheet del Dashboard
2. Extensiones → Apps Script
3. O ir directamente a script.google.com y buscar
   "Reporte de transmisiones encu..."
```

---

## 3. Pestañas del Dashboard

### `Moderno`
Cumplimiento diario para Canal Moderno. Una fila por evaluador+PDV+fecha planificada.

| Columna | Descripción |
|---|---|
| Evaluador | Nombre del mercaderista |
| PDV | Punto de venta |
| Cadena | Cadena (Favorita, TIA, etc.) |
| Fecha | Fecha planificada del rutero |
| Participación | ✅/❌ si envió encuesta ese día |
| Novedades | ✅/❌ |
| Agotados | ✅/❌ |
| Leche UHT | ✅/- (solo Favorita) |
| Devoluciones | ✅/- (no aplica Rosado/TIA) |
| Cumplió | ✅ si envió al menos una encuesta |

### `Tradicional`
Mismo formato que Moderno pero para Canal Tradicional.

### `Moderno_Semana`
Cumplimiento **semanal** — agrupa por semana ISO.

**Criterio:** ✅ si el evaluador envió al menos UNA encuesta para ese PDV en esa semana.

Útil para ver el panorama semanal sin el ruido del día a día.

### `Resumen_Mensual`
Una fila por evaluador con totales del mes:
- PDVs planificados vs visitados
- % de cumplimiento
- Encuestas enviadas por tipo

### `Resumen_Diario`
Conteo de encuestas enviadas por día. Útil para ver picos y valles de actividad.

### `Fuera_de_Fecha`
Transmisiones que llegaron **fuera del período planificado** — por ejemplo, encuestas enviadas en fin de semana o en fechas que no corresponden al rutero.

### `Validacion_Registros`
**Pestaña interna** — la App de Validación lee de aquí.

Contiene un registro por visita (deduplicado) con el estado de validación (Pendiente/Válido/Inválido).

### `Resumen_Encuestas`
Conteo de encuestas enviadas por evaluador y tipo durante el mes.

---

## 4. Cómo funciona generarDashboard

La función `generarDashboard()` es el corazón del sistema. Hace todo esto en secuencia:

```
1. Lee personal del Sheet (EVALUADOR tab)
2. Lee rutero Moderno (archivo YYYY_MM_Moderno.xlsx)
3. Lee rutero Tradicional (archivo YYYY_MM_Tradicional.xlsx)
4. Lee transmisiones de cada encuesta (Participación, Novedades, etc.)
5. Cruza rutero vs transmisiones → escribe pestaña Moderno
6. Mismo cruce para Tradicional
7. Genera Resumen_Mensual
8. Genera Resumen_Diario
9. Identifica Fuera_de_Fecha
10. Compila Validacion_Registros (para App de Validación)
11. Genera Resumen_Encuestas
12. Genera Moderno_Semana
13. Reconstruye Reporte_Participación_Moderno (reporte externo)
```

**Tiempo aproximado:** 1-2 minutos

**Para ejecutar manualmente:**
```
Apps Script del Dashboard → seleccionar generarDashboard → Ejecutar
```

---

## 5. Trigger horario

El Dashboard se actualiza automáticamente cada hora gracias a un trigger instalado.

### Verificar que el trigger está activo

```
1. Abrir Apps Script del Dashboard
2. Click en el ícono del reloj ⏱ (Triggers/Activadores)
3. Debe aparecer: generarDashboard | A partir del tiempo | Cada hora
```

### Si el trigger no aparece — reinstalarlo

```javascript
// Ejecutar esta función UNA SOLA VEZ para instalar el trigger
function instalarTriggerHorario() {
  ScriptApp.newTrigger('generarDashboard')
    .timeBased()
    .everyHours(1)
    .create();
}
```

### Monitorear ejecuciones

```
Apps Script → Ejecuciones (ícono ⏱ izquierdo)
```

Si ves muchos "Tiempo de espera agotado" consecutivos, el script está tardando más de 6 minutos. Causas comunes:
- Agotados tiene demasiados registros (>6000 filas)
- Muchas fotos que procesar
- El reporte de Participación tardó demasiado

---

## 6. Mantenimiento Mensual

Al inicio de cada mes se deben seguir estos pasos:

### Paso 1 — Subir ruteros

Los ruteros son archivos Excel con las visitas planificadas para el mes.

**Formato del nombre:** `YYYY_MM_Moderno.xlsx` y `YYYY_MM_Tradicional.xlsx`

**Dónde subirlos:** Carpeta en Google Drive que el script lee automáticamente.

```
⚠️ Si no se suben los ruteros, el Dashboard no sabrá qué visitas
   están planificadas y todo mostrará como no cumplido.
```

**Columnas requeridas en el rutero:**

| Columna | Descripción |
|---|---|
| UserVisitador | Email o nombre del evaluador (debe coincidir con EVALUADOR tab) |
| PDV | Nombre del punto de venta |
| Cadena | Nombre de la cadena |
| Fecha | Fecha planificada (formato fecha Excel) |
| Ciudad/Sector | (opcional) |

### Paso 2 — Ejecutar generarDashboard

Después de subir los ruteros, ejecutar manualmente para verificar que todo carga bien:

```
1. Apps Script del Dashboard
2. Seleccionar función: generarDashboard
3. Ejecutar
4. Revisar logs — no debe haber errores
5. Verificar que Moderno y Tradicional tienen filas
```

### Paso 3 — Verificar Validacion_Registros

```
1. Abrir el Sheet del Dashboard
2. Ir a pestaña Validacion_Registros
3. Verificar que tiene registros del mes nuevo
4. Si está vacía → ejecutar generarDashboard nuevamente
```

### Paso 4 — Actualizar Objetivo_Mes en Abbott (si aplica)

Si cambian los objetivos de visitas para Abbott:
```
1. Abrir Sheet de Abbott Cumplimiento
2. Ir a pestaña Objetivo_Mes
3. Actualizar los objetivos por evaluador/cadena/categoría
```

---

## 7. Ruteros Mensuales

### ¿Qué es un rutero?

Un archivo Excel con todas las visitas planificadas para el mes. El sistema lo usa para:
- Saber qué evaluador debe visitar qué PDV en qué fecha
- Calcular el % de cumplimiento
- Identificar visitas no realizadas

### Estructura del archivo

```
Hoja principal con estas columnas mínimas:
- UserVisitador  → nombre/email del evaluador
- PDV            → nombre del punto de venta
- Cadena         → cadena comercial
- Fecha          → fecha de la visita planificada
```

### ⚠️ Punto crítico — Nombres de evaluadores

El nombre en `UserVisitador` del rutero **debe coincidir exactamente** con el nombre que el evaluador tiene en la pestaña `EVALUADOR` del Sheet de encuestas.

Si hay diferencia (ej: "KAREN JARAMILLO" vs "Karen Jaramillo"), el evaluador no aparecerá en el Dashboard.

**Cómo verificar:**
```
1. Ver cómo está guardado el nombre en el Sheet fuente de cualquier encuesta
2. Comparar con el nombre en el rutero
3. Estandarizar ambos (preferiblemente MAYÚSCULAS)
```

---

## 8. Cómo interpretar el cumplimiento

### En la pestaña `Moderno`

| Símbolo | Significado |
|---|---|
| ✅ | Envió la encuesta ese día |
| ❌ | No envió la encuesta ese día |
| `-` | No aplica (ej: UHT en cadena que no es Favorita) |

### En la pestaña `Moderno_Semana`

| Color | Significado |
|---|---|
| 🟢 Verde | Cumplió — envió al menos una encuesta en la semana |
| 🔴 Rojo | No cumplió — ninguna encuesta enviada en la semana |

### % de Cumplimiento mensual

Se calcula como:
```
% Cumplimiento = (PDVs con al menos 1 encuesta) / (PDVs planificados) × 100
```

### Fuera de Fecha

Son transmisiones válidas pero que llegaron en una fecha diferente a la planificada. Pueden indicar:
- El evaluador fue un día diferente al planificado
- Error en la configuración del teléfono (fecha incorrecta)
- Encuesta enviada con retraso

---

*Última actualización: Julio 2026*
