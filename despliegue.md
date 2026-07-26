# 🚀 Guía de Despliegue y Cambios

> Esta guía explica cómo hacer cambios al sistema GBS Encuestas y cómo aplicarlos correctamente sin interrumpir el servicio.

---

## Índice

1. [Tipos de archivos y dónde viven](#1-tipos-de-archivos-y-dónde-viven)
2. [Cómo modificar una encuesta HTML](#2-cómo-modificar-una-encuesta-html)
3. [Cómo modificar un script Apps Script .gs](#3-cómo-modificar-un-script-apps-script-gs)
4. [Cómo actualizar el menú GitHub Pages](#4-cómo-actualizar-el-menú-github-pages)
5. [Cuándo necesitas Nueva Versión de deploy](#5-cuándo-necesitas-nueva-versión-de-deploy)
6. [Desplegar una encuesta nueva desde cero](#6-desplegar-una-encuesta-nueva-desde-cero)
7. [Errores comunes y cómo resolverlos](#7-errores-comunes-y-cómo-resolverlos)

---

## 1. Tipos de archivos y dónde viven

El sistema tiene dos tipos de archivos:

| Tipo | Extensión | Dónde se edita | Requiere deploy |
|---|---|---|---|
| **Lógica backend** | `.gs` | Google Apps Script | No (salvo web apps) |
| **Interfaz encuesta** | `.html` | Google Apps Script | **Sí** (siempre) |
| **Menú / Portales** | `.html` | GitHub | No (automático) |

### Proyectos Apps Script existentes

| Proyecto | Función | Cuenta |
|---|---|---|
| `Encuesta_Participacion_RL_(Moderno)` | Participación Canal Moderno | `gbsplanos@gmail.com` |
| `Encuesta_Agotados` | Agotados Canal Moderno | `gbsplanos@gmail.com` |
| `Encuesta_Novedades` | Novedades Canal Moderno | `gbsplanos@gmail.com` |
| `Encuesta_Devoluciones` | Devoluciones Canal Moderno | `gbsplanos@gmail.com` |
| `Encuesta_LecheUHT` | Leche UHT Maxi | `gbsplanos@gmail.com` |
| `Encuesta_Precios_RL` | Precios Rey Lácteos | `gbsplanos@gmail.com` |
| `Encuesta_Participacion_Tradicional` | Participación Tradicional | `gbsplanos@gmail.com` |
| `Reporte de transmisiones encu...` | Dashboard + generarDashboard | `gbsplanos@gmail.com` |
| `Validaciones_gbs_RL` | App de Validación | `montufar.angel@gmail.com` |

---

## 2. Cómo modificar una encuesta HTML

Las encuestas tienen DOS archivos en Apps Script:
- `Code.gs` — lógica del servidor (guarda datos, lee Sheets)
- `Index.html` — lo que ve el evaluador en su celular

### Pasos para modificar el HTML de una encuesta

```
1. Abre script.google.com
2. Busca el proyecto de la encuesta (ej: Encuesta_Precios_RL)
3. Click en el archivo Index.html
4. Haz los cambios necesarios
5. Guarda con Ctrl+S
6. Click en "Implementar" → "Administrar implementaciones"
7. Click en el lápiz ✏️ de la implementación activa
8. En "Versión" selecciona "Nueva versión"
9. Click "Implementar"
10. La URL NO cambia — los evaluadores no necesitan hacer nada
```

> ⚠️ **IMPORTANTE:** Si cambias el HTML y NO despliegas nueva versión, los evaluadores seguirán viendo la versión anterior. Siempre despliega después de cambiar el HTML.

### Pasos para modificar el Code.gs de una encuesta

```
1. Abre el proyecto en Apps Script
2. Click en Code.gs
3. Haz los cambios
4. Guarda con Ctrl+S
5. ¡Listo! No necesitas nueva versión para el .gs
   (El .gs aplica inmediatamente al guardar)
```

---

## 3. Cómo modificar un script Apps Script .gs

### Scripts que NO son web apps (solo lógica)

Por ejemplo: `Control_Transmisiones.gs`, `Code_Correcciones_Participacion.gs`

```
1. Abre el proyecto Apps Script correspondiente
2. Localiza el archivo .gs a modificar
3. Haz los cambios
4. Ctrl+S para guardar
5. Listo — aplica de inmediato
```

### Scripts que SÍ son web apps (tienen doGet)

Por ejemplo: `Code_Validacion.gs`, cualquier `Code.gs` de encuesta

- Si cambias **solo el .gs** → guarda y listo, no necesita nuevo deploy
- Si cambias **el .html** → siempre necesitas nueva versión

---

## 4. Cómo actualizar el menú GitHub Pages

El menú de encuestas y el portal de reportes viven en GitHub:

**Repositorio:** `github.com/gbsplanos-dotcom/encuestas-gbs`

### Para modificar un archivo existente

```
1. Ve al repositorio en GitHub
2. Click en el archivo que quieres modificar (ej: index.html)
3. Click en el lápiz ✏️ (Edit this file)
4. Haz los cambios
5. Scroll abajo → "Commit changes"
6. Escribe un mensaje descriptivo (ej: "Agregar nueva encuesta al menú")
7. Click "Commit changes"
8. GitHub Pages actualiza automáticamente en 1-2 minutos
```

### Para subir un archivo nuevo

```
1. Ve al repositorio
2. Click "Add file" → "Upload files"
3. Arrastra el archivo o selecciónalo
4. Commit changes
5. Listo en 1-2 minutos
```

### Archivos del repositorio

| Archivo | Qué es |
|---|---|
| `index.html` | Menú principal de encuestas |
| `tradicional.html` | Menú Canal Tradicional |
| `reportes.html` | Portal de reportes para cliente |
| `reportes-interno.html` | Portal de reportes uso interno |
| `abbott/index.html` | Menú encuestas Abbott |
| `docs/README.md` | Documentación del sistema |

---

## 5. Cuándo necesitas Nueva Versión de deploy

Esta es la regla más importante. Memorízala:

| Cambio | ¿Necesita nueva versión? |
|---|---|
| Modificar `.gs` (backend) | ❌ No |
| Modificar `.html` (encuesta) | ✅ **Sí siempre** |
| Agregar función en `.gs` | ❌ No |
| Cambiar texto/botón en `.html` | ✅ **Sí siempre** |
| Modificar `index.html` de GitHub | ❌ No (push directo) |

### Cómo hacer Nueva Versión

```
Implementar
  └── Administrar implementaciones
        └── ✏️ (lápiz editar)
              └── Versión: "Nueva versión"
                    └── Implementar
```

> La URL del deploy **no cambia**. Los evaluadores no notan nada.

---

## 6. Desplegar una encuesta nueva desde cero

Si en el futuro necesitas crear una encuesta completamente nueva:

### Paso 1 — Crear el proyecto Apps Script

```
1. Ve a script.google.com
2. Click "+ Nuevo proyecto"
3. Ponle nombre (ej: Encuesta_NuevaTienda)
4. En Código.gs pega el backend (Code.gs)
5. Click + → HTML → nómbralo "Index" (sin .html)
6. Pega el HTML de la encuesta
7. Guarda todo
```

### Paso 2 — Configurar el Sheet de destino

```
1. Crea un Google Sheet nuevo o usa uno existente
2. Crea las pestañas necesarias (Encuestas, Evaluadores, PDV, etc.)
3. Copia el Sheet ID de la URL:
   docs.google.com/spreadsheets/d/[ESTE_ES_EL_ID]/edit
4. Pega ese ID en el Code.gs donde dice SPREADSHEET_ID
```

### Paso 3 — Desplegar

```
1. Click "Implementar" → "Nueva implementación"
2. Tipo: Aplicación web
3. Ejecutar como: Yo (gbsplanos@gmail.com)
4. Quién tiene acceso: Cualquier usuario
5. Click "Implementar"
6. Autoriza los permisos que pida
7. Copia la URL generada
```

### Paso 4 — Agregar al menú

```
1. Abre index.html en GitHub
2. Agrega una tarjeta nueva con la URL copiada
3. Commit changes
```

### Paso 5 — Instalar triggers si es necesario

Algunas encuestas tienen triggers de reconstrucción automática:

```javascript
// Ejecutar UNA VEZ para instalar el trigger
function instalarTriggerCorrecciones() { ... }
```

---

## 7. Errores comunes y cómo resolverlos

### ❌ "No puedes borrar todas las filas móviles"

**Causa:** El script intenta borrar todas las filas de un Sheet con filas congeladas.

**Solución:** Usar `clearContents()` en lugar de `deleteRows()`.

```javascript
// ❌ Incorrecto
sheet.deleteRows(2, sheet.getLastRow() - 1);

// ✅ Correcto
sheet.clearContents();
sheet.clearFormats();
```

---

### ❌ "Se ha superado el tiempo máximo de ejecución"

**Causa:** El script tarda más de 6 minutos (límite de Apps Script).

**Soluciones:**
- Escribir datos en lotes de 2000 filas en vez de todos juntos
- Eliminar lecturas/escrituras individuales dentro de loops
- Separar operaciones pesadas en funciones distintas

```javascript
// ❌ Lento — escribe fila por fila
datos.forEach(function(r) { sheet.appendRow(r); });

// ✅ Rápido — escribe todo de una vez
sheet.getRange(2, 1, datos.length, cols).setValues(datos);
```

---

### ❌ "La evaluadora no aparece en el Control de Transmisiones"

**Causa:** El nombre en `EVALUADOR` de la encuesta no coincide exactamente con el nombre en el rutero mensual.

**Solución:**
1. Ver el nombre exacto en la pestaña `Encuestas` del Sheet fuente
2. Comparar con el nombre en el archivo `YYYY_MM_Moderno.xlsx`
3. Corregir en el rutero o en la tabla de `EVALUADORES` del Sheet

---

### ❌ La encuesta muestra versión antigua después de un cambio

**Causa:** Se modificó el HTML pero no se desplegó nueva versión.

**Solución:** Implementar → Administrar implementaciones → Nueva versión → Implementar.

---

### ❌ "No existe Validacion_Registros. Ejecuta generarDashboard primero"

**Causa:** La pestaña `Validacion_Registros` no existe en el Dashboard.

**Solución:** Ejecutar `generarDashboard()` manualmente desde el Apps Script del dashboard.

---

### ❌ El reporte externo de Participación no se actualiza

**Causa:** `generarDashboard` es el que actualiza el reporte cada hora.

**Solución inmediata:** Ejecutar `generarDashboard()` manualmente o esperar al siguiente trigger horario.

---

## Contactos de Soporte

| Persona | Rol | Contacto |
|---|---|---|
| Angel Montufar | Desarrollo y mantenimiento | `amontufar@gbs-ec.com` |
| Jorge | Validación de fotos | `jorgepunk1990@gmail.com` |

---

*Última actualización: Julio 2026*
