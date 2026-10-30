# Pedidos y conciliación — puente a .NET

**Estado: propuesta; no hay código implementado todavía.** Este README está listo para usar como plan inicial. Los comandos de aplicación describen el contrato que se implementará; no presentarlos como instalables hasta crear los proyectos y scripts indicados.

## Problema y valor para selección

Evitar pedidos duplicados, registrar cambios de estado y detectar diferencias entre pedidos y sus cobros simulados. Complementa Factura Lab y aporta evidencia de C# y SQL Server, presentes en varias fichas locales revisadas. No implica experiencia laboral con .NET ni equivalencia con un ERP real.

## Tecnologías

C# y ASP.NET Core en versión LTS soportada que se compruebe al iniciar; SQL Server Developer para pruebas, EF Core, xUnit; React/TypeScript para UI y Docker. Usar el mismo contrato OpenAPI de conceptos conocidos en Node para facilitar el aprendizaje. C# es una competencia nueva, no sustituirla por Node y afirmar que se aprendió .NET. SQL Server Developer es para desarrollo, no licencia de producción.

## Arquitectura y carpetas objetivo

```text
React → ASP.NET Core → aplicación/dominio → EF Core → SQL Server
src/
  Api/                 endpoints, OpenAPI y auth
  Application/         crear pedido, cambiar estado, conciliar
  Domain/              Pedido, Partida, transición de estado
  Infrastructure/      DbContext, migraciones, adaptadores
web/                   React/TypeScript
contracts/             openapi.json
tests/
  Unit/                importes y transiciones
  Integration/         SQL Server y API
  e2e/                 Playwright
docs/                  capturas, decisiones y esquema de datos
```

Mantenerlo como monolito modular. Órdenes y partidas normalizadas, índice único para idempotencia, transacción al crear pedido, migraciones revisadas y consultas de conciliación parametrizadas. Guardar importes como decimal de precisión explícita o unidades menores, nunca float. Sin pagos reales.

## Instalación objetivo

Requisitos futuros: SDK .NET LTS, Node 24, Docker y host compatible con imagen SQL Server (comprobar arquitectura y memoria; no asumir soporte ARM). Registrar versiones exactas al implementar.

```bash
# Solo después de crear compose.yaml, solución y proyectos:
docker compose up -d --wait
dotnet restore
dotnet ef database update --project src/Infrastructure --startup-project src/Api
dotnet run --project src/Api
# En otra terminal:
cd web
npm ci
npm run dev
```

La futura configuración local deberá ir en user-secrets o variables; no incluir contraseñas reales en Git. Documentar aceptación de licencia de SQL Server y limitar el puerto a loopback. Si falta Docker compatible, usar instancia de desarrollo autorizada: no degradar las pruebas reales a mocks y llamarlas integración.

## Pruebas objetivo

```bash
dotnet test
cd web
npm ci
npm test
npx playwright test
```

Cubrir transición inválida, doble envío concurrente, rollback cuando falla una partida, precisión decimal, ordenamiento/paginación y acceso a pedidos de otro usuario. En CI, SQL Server como servicio de prueba, build, unitarias, integración y reporte Playwright. Swagger/OpenAPI de rutas y respuestas de error. Hasta que el workflow corra en GitHub, no añadir badge de éxito.

## Fases (8–10 h/semana)

1. C# y API CRUD pequeña, 10–14 h.
2. Modelo relacional, EF Core y migraciones, 12–18 h.
3. Estados, idempotencia y conciliación SQL, 12–18 h.
4. Pruebas e interfaz React mínima, 16–24 h.
5. Docker, CI, OpenAPI y documentación, 8–12 h.

Total orientativo: 58–86 h, unas 6–11 semanas. Acotar a un flujo pedido → cobro simulado → conciliación.

## Odoo: extensión opcional y separada

Solo si una vacante objetivo lo justifica: 16–24 h adicionales para un adaptador de intercambio con una instancia de demostración de Odoo, con API soportada por la versión/edición elegida y pruebas de contrato. Usar Python si se desarrolla un módulo Odoo real; React/Node no equivalen a desarrollar módulos Odoo. Documentar versión, licencia, permisos y límites de API antes de integrar. No usar FEL como si estuviera resuelta por esta integración.

## Capturas y decisiones

Capturas pendientes: pedido, detalle de conciliación, respuesta 409 y reporte de pruebas. Adjuntar imágenes reales en docs/screenshots. Decisiones por documentar: EF Core frente a SQL manual, modelo de importes, concurrencia y soporte del motor.

## Evidencias y commits

Ejemplos: `feat: create order transaction`, `test: reject invalid state transition`, `feat: reconcile simulated payments`, `docs: explain sql indexes`. PR con problema, cambio y pruebas; incidencias con pasos reproducibles. Añadir README corto en inglés para trabajo remoto.

## Línea de CV al terminar

«Desarrollé una API de pedidos en C# y ASP.NET Core con SQL Server, con transacciones, conciliación de cobros simulados y pruebas automatizadas». Solo usar si esas funciones están implementadas y verificadas; agregar el enlace real creado posteriormente.
