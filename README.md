# BMC Dashboard 2.0

> **Dashboard operativo y financiero para BMC Uruguay** — integra Google Sheets con una interfaz web moderna para gestión de operaciones, finanzas, ventas e inventario.

<!-- AUTO-UPDATED:BADGES -->
![Última actualización](https://img.shields.io/badge/última_actualización-2026--03--17-blue)
![Archivos fuente](https://img.shields.io/badge/archivos_fuente-4-green)
![Líneas de código](https://img.shields.io/badge/líneas_de_código-4060-orange)
<!-- END:BADGES -->

---

## 📋 Tabla de contenidos

- [Descripción general](#descripción-general)
- [Características principales](#características-principales)
- [Arquitectura](#arquitectura)
- [Requisitos](#requisitos)
- [Instalación y configuración](#instalación-y-configuración)
- [Uso](#uso)
- [API Reference](#api-reference)
- [Frontend — Módulos del dashboard](#frontend--módulos-del-dashboard)
- [Variables de entorno](#variables-de-entorno)
- [Google Sheets — Estructura requerida](#google-sheets--estructura-requerida)
- [Desarrollo](#desarrollo)
- [Despliegue](#despliegue)
- [Documentación adicional](#documentación-adicional)
- [Estado del repositorio](#estado-del-repositorio)

---

## Descripción general

**BMC Dashboard 2.0** es un sistema de gestión operativa y financiera para BMC Uruguay que conecta múltiples planillas de Google Sheets con un dashboard web interactivo.

El proyecto está estructurado en fases:

| Fase | Descripción | Tecnología |
|------|-------------|------------|
| **Fase 1** | Automatización en Google Sheets (triggers, onEdit, alertas) | Google Apps Script |
| **Fase 2** | Flujos automáticos (estados, reportes, migraciones) | Google Apps Script |
| **Fase 3** | API REST + Dashboard web integrado | Node.js / Express |
| **Fase 4** | Integración completa con Calculadora Panelin | Node.js / Vite |

---

## Características principales

### 🚚 Próximas Entregas
- Listado de entregas de la semana actual (ESTADO = `Confirmado`, FECHA_ENTREGA = semana en curso)
- Botón **WhatsApp** — genera mensaje de logística listo para copiar
- Botón **Marcar entregado** — mueve el registro a la hoja *Ventas realizadas y entregadas*

### 📦 Coordinación Logística
- Texto formateado listo para pegar en WhatsApp para transportistas
- Incluye: nombre del cliente, teléfono, ubicación (link o datos), nº pedido, ítems

### 💰 KPIs Financieros
- Total pendiente por moneda (USD, UES, etc.)
- Montos por período: esta semana, próxima semana, este mes
- Calendario de vencimientos con gráfico
- Tabla de pagos pendientes
- Metas de ventas mensuales vs. realizadas

### 📊 Reporte KPI Ejecutivo
- Total pendiente (todas las monedas)
- Entregas esta semana
- Ítems con bajo stock (< 5 unidades)
- Estado de equilibrio (en objetivo / cerca / por debajo)

### 🛒 Ventas
- Lista completa de ventas con costo, margen y saldo
- Filtro por tab (Finalizadas, Por cobrar, etc.)
- Filtro por proveedor

### 📦 Stock / Inventario
- Gestión de inventario E-Commerce
- Alertas de bajo stock (< 5 unidades)
- Métricas: valor total del inventario en USD, productos totales

### 📝 Audit Log
- Registro completo de cambios con timestamp y usuario
- Búsqueda y filtrado de texto
- **Exportar a CSV**

---

## Arquitectura

```
bmc-dashboard-2.0/
├── README.md                              # Este archivo (auto-actualizable)
├── server/
│   └── routes/
│       └── bmcDashboard.js               # Router Express — toda la API del dashboard
└── docs/
    └── bmc-dashboard-modernization/
        ├── README.md                     # Guía de implementación detallada
        ├── DASHBOARD-INTERFACE-MAP.md    # Mapa de componentes UI
        ├── PUERTOS-3849-VS-3001.md       # Configuración de puertos
        ├── dependencies.md               # Grafo de dependencias
        ├── service-map.md                # Inventario de servicios
        └── dashboard/
            ├── index.html                # HTML del dashboard (357 líneas)
            ├── app.js                    # JavaScript del dashboard (1 239 líneas)
            └── styles.css               # Estilos CSS (1 099 líneas)
```

### Stack tecnológico

| Capa | Tecnología |
|------|-----------|
| **Backend** | Node.js + Express.js |
| **Frontend** | HTML5 + CSS3 + JavaScript (Vanilla) |
| **Base de datos** | Google Sheets (via Google Sheets API v4) |
| **Autenticación** | Google Cloud Service Account |
| **Integración** | `googleapis` npm package |

---

## Requisitos

- **Node.js** ≥ 16.x
- **npm** ≥ 8.x
- **Cuenta de Google Cloud** con:
  - Google Sheets API habilitada
  - Service Account con clave JSON descargada
  - El service account con acceso de *Editor* a la planilla de BMC

---

## Instalación y configuración

### 1. Clonar el repositorio (dentro del monorepo Calculadora-BMC)

```bash
git clone https://github.com/matiasportugau-ui/bmc-dashboard-2.0.git
```

### 2. Instalar dependencias

```bash
npm install
```

> Las dependencias principales son `express` y `googleapis`. Consultar el `package.json` del monorepo padre.

### 3. Configurar credenciales de Google Cloud

1. Descargá la clave JSON de tu Service Account desde [Google Cloud Console](https://console.cloud.google.com/iam-admin/serviceaccounts).
2. Colocá el archivo en `docs/bmc-dashboard-modernization/service-account.json`
   **O** apuntá a la ruta con la variable de entorno (ver sección [Variables de entorno](#variables-de-entorno)).
3. Compartí tu planilla de Google Sheets con el email del service account (`...@...iam.gserviceaccount.com`) como **Editor**.

### 4. Configurar variables de entorno

Creá un archivo `.env` en la raíz del proyecto:

```bash
cp .env.example .env   # si existe el ejemplo
# o creá el archivo manualmente
```

Completá las variables (ver [Variables de entorno](#variables-de-entorno)).

---

## Uso

### Iniciar el dashboard standalone (puerto 3849)

```bash
npm run bmc-dashboard
```

Abrí → [http://localhost:3849/](http://localhost:3849/)

### Iniciar la API integrada (puerto 3001)

```bash
npm run start:api
```

Abrí → [http://localhost:3001/finanzas](http://localhost:3001/finanzas)

### Modo desarrollo con hot reload

```bash
npm run dev:full
```

Levanta:
- API en `http://localhost:3001`
- Calculadora Vite en `http://localhost:5173`

| Puerto | Comando | Descripción |
|--------|---------|-------------|
| `3001` | `npm run start:api` \| `npm run dev:full` | API + Dashboard canónico |
| `3849` | `npm run bmc-dashboard` | Dashboard standalone |
| `5173` | `npm run dev` | Calculadora React (Vite) |
| `4040` | ngrok (opcional) | Tunnel para OAuth |

---

## API Reference

Todas las rutas están montadas bajo el router `bmcDashboard.js`.

### Operaciones

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/cotizaciones` | Lista todas las cotizaciones (CRM_Operativo o Master_Cotizaciones) |
| `POST` | `/api/cotizaciones` | Crea una nueva cotización (solo schema CRM_Operativo) |
| `PATCH` | `/api/cotizaciones/:id` | Actualiza una cotización existente |
| `GET` | `/api/proximas-entregas` | Entregas de la semana actual (ESTADO=Confirmado) |
| `POST` | `/api/marcar-entregado` | Marca una entrega como realizada (solo schema Master) |
| `GET` | `/api/coordinacion-logistica` | Texto de coordinación logística para WhatsApp |

### Finanzas

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/kpi-financiero` | KPIs financieros completos (byCurrency, calendar, pendingPayments) |
| `GET` | `/api/pagos-pendientes` | Array de pagos pendientes |
| `POST` | `/api/pagos` | Crea un nuevo pago |
| `PATCH` | `/api/pagos/:id` | Actualiza un pago existente |
| `GET` | `/api/metas-ventas` | Metas de ventas mensuales |
| `GET` | `/api/calendario-vencimientos` | Calendario de fechas de vencimiento |
| `GET` | `/api/kpi-report` | Reporte KPI ejecutivo consolidado |

### Ventas

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/ventas` | Todas las ventas (merged de múltiples tabs) |
| `GET` | `/api/ventas?tab=Finalizadas` | Ventas de un tab específico |
| `GET` | `/api/ventas?proveedor=X` | Ventas filtradas por proveedor |
| `GET` | `/api/ventas/tabs` | Lista los tabs disponibles |
| `POST` | `/api/ventas` | Registra una nueva venta |

### Stock

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/stock-ecommerce` | Datos completos de stock |
| `GET` | `/api/stock-kpi` | Resumen de bajo stock |
| `GET` | `/api/stock/history` | Historial de existencias y egresos |
| `PATCH` | `/api/stock/:codigo` | Actualiza nivel de stock |

### Audit & Misc

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/audit` | Registro de cambios (AUDIT_LOG) |
| `GET` | `/health` | Health check del servidor y Sheets API |

---

## Frontend — Módulos del dashboard

El dashboard (`docs/bmc-dashboard-modernization/dashboard/`) está construido con HTML + CSS + JavaScript puro.

### Secciones de la UI

| Tab | Descripción |
|-----|-------------|
| **Inicio** | KPI Report ejecutivo |
| **Operaciones** | Próximas entregas + Coordinación logística |
| **Finanzas** | KPIs financieros, calendario de vencimientos, pagos pendientes |
| **Ventas** | Lista de ventas con filtros |
| **Stock** | Inventario con alertas de bajo stock |
| **Audit Log** | Registro de cambios con exportación CSV |

### Componentes UI

- **Cards KPI** — métricas grandes y destacadas
- **Tablas dinámicas** — renderizadas con datos de la API
- **Selector de moneda** — filtra KPIs por divisa
- **Notificaciones toast** — desaparecen automáticamente
- **Banner de estado** — loading / error con botón de reintentar
- **Botón de actualización manual** — con timestamp de última actualización
- **Exportar CSV** — generación de reportes descargables

---

## Variables de entorno

Creá un archivo `.env` en la raíz del proyecto con las siguientes variables:

```bash
# ─── Google Cloud / Sheets API (REQUERIDO) ─────────────────────────────────

# Ruta absoluta o relativa al JSON del Service Account de Google Cloud.
# El service account debe tener habilitada la Sheets API y acceso Editor a la planilla.
# Ruta por defecto: docs/bmc-dashboard-modernization/service-account.json
GOOGLE_APPLICATION_CREDENTIALS=/ruta/a/service-account.json

# ID de la planilla principal de BMC.
# Se obtiene de la URL: https://docs.google.com/spreadsheets/d/<BMC_SHEET_ID>/edit
BMC_SHEET_ID=<id-de-la-planilla>

# Schema de la planilla: "CRM_Operativo" o "Master_Cotizaciones"
BMC_SHEET_SCHEMA=CRM_Operativo

# ─── IDs alternativos por módulo (OPCIONAL) ────────────────────────────────

BMC_CALENDAR_SHEET_ID=   # Hoja de Pagos_Pendientes (ubicación alternativa)
BMC_PAGOS_SHEET_ID=      # Hoja dedicada de pagos
BMC_VENTAS_SHEET_ID=     # Hoja de ventas (2.0 - Ventas)
BMC_STOCK_SHEET_ID=      # Hoja de inventario

# ─── Calculadora / Vite (OPCIONAL) ─────────────────────────────────────────

VITE_API_URL=http://localhost:3001
VITE_GOOGLE_CLIENT_ID=<google-oauth-client-id>
```

---

## Google Sheets — Estructura requerida

### Hojas requeridas

| Hoja | Uso | Degradación |
|------|-----|-------------|
| `Master_Cotizaciones` o `CRM_Operativo` | Cotizaciones | Falla si ninguna existe |
| `Pagos_Pendientes` | KPIs financieros | Degrada limpio |
| `AUDIT_LOG` | Registro de cambios | Degrada limpio |
| `Metas_Ventas` | Metas de ventas | Degrada limpio (opcional) |
| `Calendario de Vencimientos` | Fechas de pago | Degrada limpio (opcional) |
| `EXISTENCIAS_Y_PEDIDOS` | Stock | Degrada limpio (opcional) |
| `Ventas realizadas y entregadas` | Destino de entregas | Requerida para *marcar-entregado* |

### Schema CRM_Operativo (columnas principales)

```
ID | Fecha | Cliente | Teléfono | Dirección | Estado | Asignado_A | Fecha_Entrega |
Nro_Pedido | Items | Notas | Link_Ubicacion | Link_Cotizacion
```

### Schema Master_Cotizaciones (columnas principales)

```
ID | Fecha | Cliente | Teléfono | Dirección | ESTADO | Asignado_A | FECHA_ENTREGA |
NÚMERO_PEDIDO | ITEMS | NOTAS | LINK_UBICACION | LINK_COTIZACION
```

---

## Desarrollo

### Estructura del backend (`bmcDashboard.js`)

El router principal exporta todas las rutas `/api/*` y gestiona:

- Autenticación con Google Sheets API via Service Account
- Transformación y normalización de datos de múltiples hojas
- Lógica de negocio (cálculos financieros, entregas, stock)
- Escritura en Sheets (crear, actualizar, mover filas)

**Funciones utilitarias clave:**

| Función | Descripción |
|---------|-------------|
| `getStartOfWeek()` / `getEndOfWeek()` | Cálculo de fechas de la semana |
| `getResumenPagosPorPeriodo()` | Resumen financiero por período |
| `buildWhatsAppBlock()` | Formatea mensaje de WhatsApp por pedido |
| `buildCoordinacionLogisticaText()` | Genera texto de coordinación en bloque |

### Modo live reload (desarrollo)

```bash
npm run dev:full
# API en 3001 + Vite en 5173
# Editá dashboard/index.html, app.js, styles.css y guardá
# El dashboard hace live reload automático cada ~2s en ?dev=1
```

Detalle completo: [`LIVE-EDITING.md`](docs/bmc-dashboard-modernization/LIVE-EDITING.md)

---

## Despliegue

### VPS / Netuy

```bash
# Clonar y configurar .env en el servidor
git clone https://github.com/matiasportugau-ui/bmc-dashboard-2.0.git
cd bmc-dashboard-2.0
npm install
cp .env.production .env   # configurar credenciales reales

# Iniciar con PM2 (recomendado)
npm install -g pm2
pm2 start "npm run start:api" --name bmc-dashboard
pm2 save
pm2 startup
```

Guía completa: [`HOSTING-EN-MI-SERVIDOR.md`](docs/bmc-dashboard-modernization/HOSTING-EN-MI-SERVIDOR.md)

### ngrok (exposición temporal para OAuth)

```bash
ngrok http 3001
# Copia la URL HTTPS y configúrala en Google Cloud OAuth redirect URIs
```

---

## Documentación adicional

| Documento | Descripción |
|-----------|-------------|
| [`docs/bmc-dashboard-modernization/README.md`](docs/bmc-dashboard-modernization/README.md) | Guía de implementación completa (Fases 1–4) |
| [`docs/bmc-dashboard-modernization/DASHBOARD-INTERFACE-MAP.md`](docs/bmc-dashboard-modernization/DASHBOARD-INTERFACE-MAP.md) | Mapa de componentes e interfaz UI |
| [`docs/bmc-dashboard-modernization/PUERTOS-3849-VS-3001.md`](docs/bmc-dashboard-modernization/PUERTOS-3849-VS-3001.md) | Cuándo usar puerto 3001 vs 3849 |
| [`docs/bmc-dashboard-modernization/dependencies.md`](docs/bmc-dashboard-modernization/dependencies.md) | Grafo de dependencias por módulo |
| [`docs/bmc-dashboard-modernization/service-map.md`](docs/bmc-dashboard-modernization/service-map.md) | Inventario de servicios y contratos API |
| [`docs/google-sheets-module/planilla-inventory.md`](docs/google-sheets-module/planilla-inventory.md) | Estructura de las planillas de Google Sheets |

---

## Estado del repositorio

<!-- AUTO-UPDATED:STATS -->
| Métrica | Valor |
|---------|-------|
| Última actualización | 2026-03-17 |
| Commit más reciente | `b7f0cff` — Merge pull request #1 from matiasportugau-ui/copilot/create-comprehensiv |
| Archivos fuente | 4 |
| Líneas de código (aprox.) | 4060 |
| Backend (rutas API) | 1365 líneas |
| Frontend JS | 1239 líneas |
| Frontend HTML | 357 líneas |
| Frontend CSS | 1099 líneas |
<!-- END:STATS -->

---

*Este README es actualizado automáticamente en cada push mediante el workflow [`.github/workflows/update-readme.yml`](.github/workflows/update-readme.yml).*