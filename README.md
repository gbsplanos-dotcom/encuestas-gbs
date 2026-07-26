# 📋 GBS Encuestas — Documentación del Sistema

> **Sistema de encuestas de campo para Rey Lácteos y Abbott**
> Desarrollado y mantenido por el equipo técnico de GBS Ecuador

---

## 📌 Índice General

| Sección | Descripción |
|---|---|
| [1. Arquitectura General](#1-arquitectura-general) | Stack tecnológico y flujo de datos |
| [2. Encuestas Rey Lácteos Moderno](#2-encuestas-rey-lácteos-moderno) | Canal moderno — 6 encuestas activas |
| [3. Encuestas Rey Lácteos Tradicional](#3-encuestas-rey-lácteos-tradicional) | Canal tradicional — Participación |
| [4. Encuestas Abbott](#4-encuestas-abbott) | Precios y cumplimiento Abbott |
| [5. Dashboard Control Transmisiones](#5-dashboard-control-transmisiones) | Dashboard de seguimiento horario |
| [6. App de Validación GBS](#6-app-de-validación-gbs) | Validación y corrección de fotos |
| [7. Reportes Externos](#7-reportes-externos) | Reportes para cliente y uso interno |
| [8. Portal de Reportes](#8-portal-de-reportes) | Menú GitHub Pages |
| [9. Mantenimiento Mensual](#9-mantenimiento-mensual) | Checklist para inicio de mes |
| [10. IDs y Referencias](#10-ids-y-referencias) | Todos los IDs de Sheets y Scripts |

---

## 1. Arquitectura General

### Stack Tecnológico

```
Evaluador en campo
      │
      ▼
Encuesta HTML (Apps Script Web App)
      │
      ▼
Google Apps Script (backend)
      │
      ├──► Google Sheets (almacenamiento)
      │         └── Encuestas / Detalle_Marcas / Reporte_*
      │
      ├──► Google Drive (fotos)
      │
      └──► Reporte Externo (Sheet separado para cliente)
```

### Componentes Principales

| Componente | Tecnología | Repositorio/Ubicación |
|---|---|---|
| Menú de encuestas | GitHub Pages | `gbsplanos-dotcom.github.io/encuestas-gbs/` |
| Encuestas | Google Apps Script (HTML + GS) | Apps Script por encuesta |
| Almacenamiento | Google Sheets | Ver sección IDs |
| Fotos | Google Drive | Carpeta `1C_PMDa4cQtbGfcHJdGbonFi70qMIe7ND` |
| Dashboard | Google Apps Script + Sheets | Sheet `1IvaFUx_...` |
| App Validación | Google Apps Script Web App | Proyecto `Validaciones_gbs_RL` |

### Flujo de Datos

```
1. Evaluador abre encuesta desde menú GitHub Pages
2. Llena formulario en el celular
3. Apps Script guarda en Sheets + foto en Drive
4. generarDashboard() corre cada hora:
   - Actualiza pestaña Moderno/Tradicional
   - Compila Validacion_Registros
   - Reconstruye Reporte_Participación_Moderno
5. Jorge valida fotos en App de Validación
6. Reportes externos disponibles para cliente
```

---

## 2. Encuestas Rey Lácteos Moderno

### URLs de Acceso

| Encuesta | URL | Sheet Fuente |
|---|---|---|
| **Menú Moderno** | `gbsplanos-dotcom.github.io/encuestas-gbs/` | — |
| Participación Percha | `script.google.com/macros/s/.../exec` | `1bJhEwVI...` |
| Agotados | `script.google.com/macros/s/.../exec` | `1A73VoF1...` |
| Novedades | `script.google.com/macros/s/.../exec` | `1g6WFpDr...` |
| Devoluciones | `script.google.com/macros/s/.../exec` | `1rQqweCZ...` |
| Leche UHT | `script.google.com/macros/s/.../exec` | `1IPsDMQJ...` |
| Precios | `script.google.com/macros/s/.../exec` | `1oTyaSEt...` |

### Encuesta de Participación en Percha

**¿Qué levanta?** Número de caras por marca y subcategoría en góndola.

**Estructura del Sheet:**
- `Encuestas` — una fila por subcategoría por visita
- `Detalle_Marcas` — una fila por marca (con ► TOTAL al final de cada subcategoría)
- `Historial_Cambios` — registro de correcciones

**Lógica de guardado:**
```
ClientId (único por envío) → agrupa todas las subcategorías de una visita
```

**Reporte externo:** `1MLqGqevHaPMbFJZsgak0xQNMvrlzw_cLQuS-FacEhQg`

---

### Encuesta de Agotados

**¿Qué levanta?** SKUs sin stock por PDV.

**Sheet fuente:** `1A73VoF13dHh-5mjZOpGPlzzitMrnZVNk2GpS8_F3a84`

**Reporte externo:** `1Zh-RIORQlW2llflVP1JsTGeZv-iPMJ32Z0K0gfUI2QE`

---

### Encuesta de Novedades

**¿Qué levanta?** Productos nuevos, promociones, visibilidad POP.

**Sheet fuente:** `1g6WFpDrPnKL8qwdlpTvzeQr_85pU8j1MMA2q6VOZ8Jo`

**Reporte externo:** `1lGlQa4OXETJLUrzjGseqVV-iRl8LqlX3HkcggjbDUsY`

---

### Encuesta de Devoluciones

**¿Qué levanta?** Devoluciones por caducidad o calidad.

**Regla:** No aplica a cadenas EL ROSADO ni TIA.

**Sheet fuente:** `1rQqweCZh-i9kFk3PE-Cf5KMS-5PsjBSuE5EaYzdot8U`

**Reporte externo:** `1eM_amCLIfpdkMGgG6TE1uxNnvvPDoiPPKJtFGejRNz8`

---

### Encuesta Leche UHT (Maxi)

**¿Qué levanta?** Caras BBL, UHT Entera y Semidescremada en percha.

**Regla:** Solo aplica a LA FAVORITA.

**Sheet fuente:** `1IPsDMQJJHjMy4H4H6UFS_DvFrf69Tcbgrv55YFF-zNg`

**Reporte externo:** `1uOv-i3rxdpVAB8rYhgfYkcM4ZsxMO-0VSfQwvANHMwg`

---

### Encuesta de Precios

**¿Qué levanta?** PVP, PEP y promociones por SKU y cadena.

**Funcionalidades especiales:**
- Borrador automático 7 días (localStorage)
- Colapso automático de SKU al completar (12 segundos)
- Botón "Solo pendientes"

**Sheet fuente:** `1oTyaSEti9ApQjccHxKP3GpxQTyPPO43wne-bSfmWSUY`

**Reporte externo:** `1AUI185Ll7sBfNdck6jle-SD_ormYvHLhJIguZpwdU4c`

---

## 3. Encuestas Rey Lácteos Tradicional

### URLs de Acceso

| Encuesta | URL | Sheet Fuente |
|---|---|---|
| **Menú Tradicional** | `gbsplanos-dotcom.github.io/encuestas-gbs/tradicional.html` | — |
| Participación Percha | `script.google.com/macros/s/AKfycby-LDdR27...` | `1QRj34CG...` |

**Sheet fuente Participación:** `1QRj34CGdTA6xCXvgJf1Z-X3ZefNn0lKycXPDy754HiI`

**Reporte externo:** `10QPYvrbMah9p_6uMazY7DMANfvA_NwNfw1z6r7_uqBU`

---

## 4. Encuestas Abbott

### URLs de Acceso

| Encuesta | URL |
|---|---|
| **Menú Abbott** | `gbsplanos-dotcom.github.io/encuestas-gbs/abbott/` |
| Precios Abbott | Apps Script deploy |

**Sheet Abbott Cumplimiento:** `1tF7QVjQRoxRR5aBSU_e8TETIajSDHv3PqjM6mi6LmQQ`

**Funcionalidades especiales Precios Abbott:**
- Borrador automático 7 días
- Colapso automático (12 segundos)
- Campo NC para productos sin código

---

## 5. Dashboard Control Transmisiones

**Sheet Dashboard:** `1IvaFUx_Ne7e0GB6EwMa5675TfxX2mp3wWJI8ReGy45U`

**Proyecto Apps Script:** `Reporte de transmisiones encu...`

**Función principal:** `generarDashboard()`

**Trigger:** Cada hora automáticamente

### Pestañas del Dashboard

| Pestaña | Contenido |
|---|---|
| `Moderno` | Cumplimiento diario por evaluador — Canal Moderno |
| `Tradicional` | Cumplimiento diario por evaluador — Canal Tradicional |
| `Moderno_Semana` | Cumplimiento semanal (al menos 1 encuesta por PDV por semana) |
| `Resumen_Mensual` | Resumen por evaluador del mes |
| `Resumen_Diario` | Detalle diario por encuesta |
| `Fuera_de_Fecha` | Transmisiones enviadas fuera del período planificado |
| `Validacion_Registros` | Registros compilados para la App de Validación |
| `Resumen_Encuestas` | Conteo de encuestas por evaluador |

### Reglas de Negocio

| Encuesta | Regla especial |
|---|---|
| Leche UHT | Solo aplica a LA FAVORITA |
| Devoluciones | No aplica a EL ROSADO ni TIA |
| Moderno_Semana | ✅ si levantó al menos 1 encuesta del PDV en la semana |

### Ruteros

Los ruteros mensuales se leen de una carpeta en Drive:
- **Moderno:** Archivo `2026_07_Moderno.xlsx` con PDVs planificados
- **Tradicional:** Archivo `2026_07_Tradicional.xlsx`

**Al iniciar un nuevo mes:** Subir los nuevos archivos de rutero a la carpeta correspondiente.

---

## 6. App de Validación GBS

**URL de acceso:** Deploy de Apps Script (proyecto `Validaciones_gbs_RL`)

**Correos autorizados:**
- `jorgepunk1990@gmail.com` (Jorge — valida fotos)
- `gbsplanos@gmail.com`
- `montufar.angel@gmail.com`

### ¿Qué hace?

Permite a Jorge revisar cada encuesta enviada:
- Ver las fotos del levantamiento
- Marcar como ✅ Válido o ❌ Inválido
- Corregir valores incorrectos (caras, SKUs, precios)
- Las correcciones se guardan en los Sheets fuente

### Flujo de Validación

```
1. generarDashboard() compila Validacion_Registros (cada hora)
2. Jorge abre App de Validación
3. Filtra por fecha, evaluador, encuesta, PDV
4. Revisa foto → marca Válido o Inválido
5. Si Inválido → panel de corrección → guarda
6. Corrección se refleja en Sheet fuente inmediatamente
7. Reporte externo se actualiza en la siguiente ejecución de generarDashboard()
```

### Encuestas que soporta

| Encuesta | Panel de corrección |
|---|---|
| Participación | Editar caras por marca |
| Agotados | Marcar SKUs para eliminar |
| Novedades | Editar campos del formulario |
| Devoluciones | Editar item y cantidad |
| Leche UHT | Editar caras BBL, Entera, Semi |

---

## 7. Reportes Externos

Los reportes externos son Sheets separados que comparten con el cliente.

### Rey Lácteos

| Reporte | Sheet ID |
|---|---|
| Participación Moderno | `1MLqGqevHaPMbFJZsgak0xQNMvrlzw_cLQuS-FacEhQg` |
| Agotados | `1Zh-RIORQlW2llflVP1JsTGeZv-iPMJ32Z0K0gfUI2QE` |
| Novedades | `1lGlQa4OXETJLUrzjGseqVV-iRl8LqlX3HkcggjbDUsY` |
| Devoluciones | `1eM_amCLIfpdkMGgG6TE1uxNnvvPDoiPPKJtFGejRNz8` |
| Leche UHT | `1uOv-i3rxdpVAB8rYhgfYkcM4ZsxMO-0VSfQwvANHMwg` |
| Participación Tradicional | `10QPYvrbMah9p_6uMazY7DMANfvA_NwNfw1z6r7_uqBU` |
| Precios Moderno | `1AUI185Ll7sBfNdck6jle-SD_ormYvHLhJIguZpwdU4c` |

### Cuándo se actualizan

| Reporte | Cuándo se actualiza |
|---|---|
| Participación Moderno | Cada hora (con `generarDashboard`) |
| Resto de encuestas | Inmediatamente al aplicar corrección en App de Validación |

---

## 8. Portal de Reportes

**GitHub Pages:** `gbsplanos-dotcom.github.io/encuestas-gbs/`

| Página | URL | Para quién |
|---|---|---|
| Menú Encuestas | `/` | Evaluadores en campo |
| Reportes Cliente | `/reportes.html` | Cliente Rey Lácteos |
| Reportes Interno | `/reportes-interno.html` | Equipo GBS |

---

## 9. Mantenimiento Mensual

Al inicio de cada mes, realizar estos pasos en orden:

### Checklist Inicio de Mes

- [ ] **1. Subir rutero Moderno** — archivo `YYYY_MM_Moderno.xlsx` a la carpeta de ruteros
- [ ] **2. Subir rutero Tradicional** — archivo `YYYY_MM_Tradicional.xlsx`
- [ ] **3. Ejecutar `generarDashboard()`** — verificar que corre sin errores
- [ ] **4. Verificar pestañas del dashboard** — Moderno, Tradicional, Semana
- [ ] **5. Actualizar Objetivo_Mes en Abbott** — si cambian los objetivos por evaluador
- [ ] **6. Verificar Validacion_Registros** — que compile correctamente las 5 encuestas
- [ ] **7. Compartir reportes externos** — enviar links al cliente si es necesario

### Dónde están los Ruteros

Los ruteros son archivos Excel con las visitas planificadas por evaluador:

```
Columnas requeridas: UserVisitador, PDV, Cadena, Fecha, etc.
Nombre del archivo: YYYY_MM_Moderno.xlsx / YYYY_MM_Tradicional.xlsx
```

---

## 10. IDs y Referencias

### Sheets Principales

| Recurso | ID |
|---|---|
| Dashboard | `1IvaFUx_Ne7e0GB6EwMa5675TfxX2mp3wWJI8ReGy45U` |
| Participación Moderno | `1bJhEwVIeZEmvaQ0Xv4aJ_7s6fO5ZX2EF5ZCtgOCoZjQ` |
| Agotados Moderno | `1A73VoF13dHh-5mjZOpGPlzzitMrnZVNk2GpS8_F3a84` |
| Novedades | `1g6WFpDrPnKL8qwdlpTvzeQr_85pU8j1MMA2q6VOZ8Jo` |
| Devoluciones | `1rQqweCZh-i9kFk3PE-Cf5KMS-5PsjBSuE5EaYzdot8U` |
| Leche UHT | `1IPsDMQJJHjMy4H4H6UFS_DvFrf69Tcbgrv55YFF-zNg` |
| Participación Tradicional | `1QRj34CGdTA6xCXvgJf1Z-X3ZefNn0lKycXPDy754HiI` |
| Precios Rey Lácteos | `1oTyaSEti9ApQjccHxKP3GpxQTyPPO43wne-bSfmWSUY` |
| Abbott Cumplimiento | `1tF7QVjQRoxRR5aBSU_e8TETIajSDHv3PqjM6mi6LmQQ` |

### Cuentas y Accesos

| Cuenta | Rol |
|---|---|
| `gbsplanos@gmail.com` | Cuenta principal de deploy |
| `amontufar@gbs-ec.com` | Interno GBS |
| `jorgepunk1990@gmail.com` | Validación de fotos (Jorge) |
| `montufar.angel@gmail.com` | App de Validación |

### Repositorio GitHub

- **Repo:** `gbsplanos-dotcom/encuestas-gbs`
- **GitHub Pages:** `https://gbsplanos-dotcom.github.io/encuestas-gbs/`
- **Rama principal:** `main`

---

*Documentación generada por GBS Ecuador — Actualizada julio 2026*
