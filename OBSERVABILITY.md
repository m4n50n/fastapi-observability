# 🧩 Observabilidad con FastAPI, OpenTelemetry, Grafana, Tempo, Loki y Prometheus

Este proyecto implementa un sistema completo de **observabilidad** para una aplicación FastAPI, utilizando las tres piezas clave: **trazas**, **métricas** y **logs**.

## 🔁 Flujo general

    🔄 Petición HTTP
     ↓
 🟦 FastAPI app (instrumentada con OpenTelemetry)
    ├── 📤 Trazas → Tempo
    ├── 📤 Métricas → Prometheus
    └── 📤 Logs → Loki (vía Docker plugin)
     ↓
 📊 Grafana (visualiza todo)


---

## 🧱 Componentes y responsabilidades

### 🟦 FastAPI (aplicación principal)

- Instrumentada con **OpenTelemetry**
- Envía:
  - **Trazas** a Tempo
  - **Métricas** a Prometheus
  - **Logs enriquecidos** (con `trace_id`, `span_id`) a Loki

### 🟡 Prometheus – Métricas

- Scrapea `/metrics` de la app
- Guarda métricas como:
  - Tiempo de respuesta
  - Número de peticiones por ruta
- **Exemplars** permiten asociar métricas con trazas (`TraceID`)

### 🔵 Tempo – Trazas

- Recibe trazas desde la app vía OpenTelemetry
- Almacena spans y contextos de trazas distribuidas
- Permite inspeccionar visualmente el recorrido completo de una petición

### 🟣 Loki – Logs

- Recibe logs directamente desde los contenedores Docker
- Extrae campos como `trace_id`, `span_id`, `service.name`
- Permite búsquedas y correlaciones por trazabilidad

### 🟢 Grafana – Visualización centralizada

- Integra:
  - Prometheus → métricas
  - Tempo → trazas
  - Loki → logs
- Permite:
  - 🔍 Navegar de métricas → trazas → logs
  - 📈 Ver dashboards
  - 🛠️ Realizar diagnósticos completos desde un solo punto

---

## 🧪 Ejemplo práctico del flujo

1. Se realiza una petición HTTP a `/chain`
2. FastAPI crea una traza con varios spans:
   - `/chain` → `/io_task` → `/cpu_task`
3. Cada llamada queda registrada como:
   - Una **métrica** con duración y `TraceID` (exemplar)
   - Un **log estructurado** con `trace_id` y `span_id`
4. Grafana permite:
   - Ver métricas por ruta o servicio
   - Clicar en `TraceID` desde métricas y logs para abrir la traza completa
   - Investigar logs relacionados con una traza

---

## ✅ Beneficios

- Observabilidad distribuida real
- Diagnóstico rápido de cuellos de botella y errores
- Fácil navegación entre métricas, trazas y logs
- Infraestructura completamente auto-contenida con Docker
