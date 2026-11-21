# Plan de Aseguramiento de Calidad de Software (SQA)
## Hotel Booking Management System — MERN

| Campo | Valor |
|---|---|
| **Documento** | `docs/PLAN_ASEGURAMIENTO_CALIDAD.md` |
| **Proyecto** | Hotel Booking Management System (React + Express + MongoDB) |
| **Versión** | 1.0 (borrador propuesto) |
| **Fecha** | AAAA-MM-DD |
| **Estado** | Borrador / En revisión / Aprobado |
| **Elaborado por** | _[Nombre / Rol]_ |
| **Revisado por** | _[Nombre / Rol]_ |
| **Aprobado por** | _[Nombre / Rol]_ |

## Control de versiones

| Versión | Fecha | Autor | Descripción del cambio |
|---|---|---|---|
| 1.0 | AAAA-MM-DD | _[Autor]_ | Versión inicial del plan |

---

## 1. Introducción

### 1.1 Propósito
Definir las actividades, criterios, recursos, cronograma, riesgos, normas y estándares para asegurar la calidad del software del proyecto **Hotel Booking Management System**, garantizando que los entregables cumplan los requisitos funcionales, no funcionales y de seguridad acordados.

### 1.2 Alcance del plan
Este plan aplica al ciclo de vida de desarrollo, prueba y liberación de los componentes del repositorio:

- **Backend** (`hotel-booking-backend/`): API REST en Express + Mongoose + TypeScript (puerto local canónico `5001`), autenticación JWT/OAuth, reglas de negocio, integraciones (MongoDB, Stripe, Cloudinary, Google OAuth, asistente IA opcional).
- **Frontend** (`hotel-booking-frontend/`): SPA React 18 + Vite + React Query + Tailwind (puerto local `5174`), flujos de usuario, consumo de API, pagos con Stripe en cliente.
- **Contratos compartidos** (`shared/types.ts`): tipos TypeScript compartidos FE/BE.
- **Pruebas E2E** (`e2e-tests/`): automatización con Playwright.
- **Documentación y despliegue**: `README.md`, `docs/*`, `SECURITY.md`, Docker (backend, build desde raíz del repo) y configuración Vercel (frontend SPA).

**Incluye:** verificación funcional, regresión, pruebas de API, smoke, seguridad básica, validación de integraciones, criterios de aceptación/liberación y gestión de defectos.  
**Excluye (salvo ampliación aprobada):** pentesting formal externo, pruebas de carga/capacidad a escala productiva, certificaciones formales, migraciones de arquitectura/stack no planificadas.

### 1.3 Objetivos de calidad
- Cumplir los requisitos funcionales y criterios de aceptación definidos (y, cuando aplique, los `REQ-XXXX` del AQMS en `.agile-v/`).
- Detectar defectos tempranamente mediante compilación, lint, smoke y pruebas automatizadas/manuales.
- Asegurar confiabilidad de flujos críticos: autenticación, búsqueda, reserva/pago/reembolso, gestión de hoteles/reservas y paneles administrativos/insights.
- Reducir riesgos de seguridad y configuración (secretos, CORS, authz, exposición de información).
- Garantizar trazabilidad entre cambios, pruebas, defectos y evidencias de liberación.

### 1.4 Referencias del proyecto
| Documento / Artefacto | Ubicación / Nota |
|---|---|
| README del proyecto | `README.md` |
| Guía de arquitectura | `docs/PROJECT_WALKTHROUGH.md` |
| Seguridad y reporte de vulnerabilidades | `SECURITY.md` |
| Instrucciones de agentes/convenciones | `AGENTS.md`, `.agile-v/PLAYBOOK.md` |
| Estado y requisitos AQMS | `.agile-v/STATE.md`, `.agile-v/REQUIREMENTS.md`, `.agile-v/CHECKPOINTS.md` |
| API interactiva (runtime) | `/api-docs` (Swagger) |
| Salud del servicio (runtime) | `/api/health`, `/api/health/detailed` (JWT) |

---

## 2. Definiciones, acrónimos y abreviaturas

| Término | Definición |
|---|---|
| SQA | Software Quality Assurance (Aseguramiento de la Calidad de Software) |
| QA / QC | Quality Assurance / Quality Control |
| E2E | End-to-End (pruebas extremo a extremo) |
| UAT | User Acceptance Testing (pruebas de aceptación de usuario) |
| API | Application Programming Interface |
| JWT | JSON Web Token |
| SPA | Single Page Application |
| PI | PaymentIntent (Stripe) |
| CI/CD | Integración continua / Despliegue continuo |
| RQ | React Query (capa de caché/sincronización de datos en FE) |
| DoR / DoD | Definition of Ready / Definition of Done |
| Severidad | Impacto técnico/negocio del defecto |
| Prioridad | Urgencia de atención/corrección |

---

## 3. Organización, roles y responsabilidades

| Rol | Responsabilidades principales | Entregables/Evidencia |
|---|---|---|
| **Product Owner / Stakeholder** | Priorizar alcance, aprobar criterios de aceptación y liberación | Aprobaciones, decisiones de alcance |
| **Líder técnico / Arquitectura** | Definir estándares técnicos, revisar riesgos y cambios críticos | Revisiones técnicas, decisiones de diseño |
| **Desarrollador Backend** | Implementar/corregir API, integraciones y reglas de negocio; pruebas de componente | Código, build OK, notas de cambio |
| **Desarrollador Frontend** | Implementar/corregir UI, flujos y manejo de estado/cache | Código, build/lint OK, evidencias UI |
| **QA / Verificador** | Diseñar/ejecutar pruebas, reportar defectos, verificar correcciones (independiente de quien construye) | Plan de pruebas, casos, resultados, reporte de defectos |
| **DevOps / Release** | Configurar entornos, secretos, despliegue y smoke post-deploy | Evidencia de despliegue, healthchecks, rollback plan |
| **Seguridad (referente)** | Revisar hallazgos de seguridad, configuración y cumplimiento de políticas | Observaciones, recomendaciones, validación de remediaciones |

> **Principio de independencia:** la verificación de liberación no debe basarse únicamente en la auto-validación de quien implementó el cambio (alineado a la práctica de verificación independiente del proyecto).

---

## 4. Normas, estándares y lineamientos aplicables

### 4.1 Normas y marcos de referencia (adoptados/adaptados)
| Norma / Estándar | Aplicación en el proyecto |
|---|---|
| **ISO/IEC 25010** (Modelo de calidad del producto) | Referencia para atributos: adecuación funcional, fiabilidad, usabilidad, eficiencia de desempeño, seguridad, compatibilidad, mantenibilidad, portabilidad |
| **ISO/IEC/IEEE 29119** (Pruebas de software) | Referencia para procesos, documentación y tipos/niveles de prueba |
| **IEEE 730** (Procesos/planes de SQA) | Referencia para estructura del plan, actividades de aseguramiento, revisiones y registros |
| **OWASP Top 10 / ASVS (referencia)** | Lineamientos para pruebas de seguridad de aplicaciones web (auth, acceso, exposición de datos, configuración) |
| **WCAG 2.1 (nivel AA, objetivo recomendado)** | Guía de accesibilidad para UI web (formularios, navegación, contraste, feedback) |

### 4.2 Estándares y convenciones internas del proyecto
| Área | Estándar / Convención |
|---|---|
| Lenguaje y tipado | TypeScript en backend y frontend; contratos compartidos en `shared/types.ts` |
| Estilo/lint (FE) | ESLint (`npm run lint` en frontend) con reglas del proyecto |
| Compilación | `npm run build` en backend y frontend como puerta mínima de calidad |
| API | Contratos REST documentados/explorables vía Swagger (`/api-docs`) |
| Gestión de configuración | Variables de entorno por paquete (`.env`, `.env.local`); **prohibido** commitear secretos |
| Seguridad operativa | Reporte privado de vulnerabilidades según `SECURITY.md`; health público sin datos sensibles; endpoints detallados protegidos |
| Trazabilidad (AQMS del repo) | Mapeo de trabajo a `REQ-XXXX` y evidencias en `.agile-v/*` cuando aplique el proceso formal |
| Control de cambios | Cambios trazables por commit/PR; cambios de alcance/requisitos gestionados y aprobados |

### 4.3 Criterios de finalización (Definition of Done — propuesta)
Un incremento se considera **Done** cuando:
- Compila sin errores (`backend build`, `frontend build`) y pasa lint FE.
- Cumple criterios de aceptación y no introduce regresiones en flujos críticos.
- Pruebas planificadas ejecutadas con evidencia (manual/automatizada según aplique).
- Defectos bloqueantes/altos resueltos o formalmente aceptados con mitigación.
- Documentación/configuración actualizada (env, despliegue, notas de cambio) cuando corresponda.

---

## 5. Estrategia y plan de pruebas

### 5.1 Niveles y tipos de prueba

| Tipo | Objetivo | Responsable | Herramientas / Método | Momento |
|---|---|---|---|---|
| **Estática / Build** | Detectar errores de tipado y compilación | Dev / QA | `cd hotel-booking-backend && npm run build`; `cd hotel-booking-frontend && npm run build` | En cada cambio |
| **Lint / Estilo** | Consistencia y reglas de código FE | Dev / QA | `cd hotel-booking-frontend && npm run lint` | En cada cambio |
| **Smoke** | Verificar disponibilidad básica tras cambios/deploy | QA / DevOps | `GET /api/health`, carga SPA, login básico | Tras build/deploy |
| **Pruebas de API** | Validar contratos, códigos HTTP, authz y payloads | QA / Backend | Swagger, Postman/curl, pruebas automatizadas (futuro) | Por feature y regresión |
| **Integración** | Validar interacción entre módulos y servicios externos | QA / Dev | MongoDB, Stripe (test), Cloudinary, OAuth (si aplica) | Por feature e integración |
| **Funcionales / Sistema** | Validar flujos de negocio end-to-end por rol | QA | Casos manuales guiados en UI + API | Por iteración/release |
| **E2E automatizadas** | Regresión de journeys críticos | QA | Playwright (`cd e2e-tests && npx playwright test`) | Regresión / nightly / CI |
| **Regresión** | Asegurar que cambios no rompan funcionalidades existentes | QA | Suite manual dirigida + E2E | Antes de release |
| **Seguridad básica** | Validar authn/authz, CORS, manejo de secretos, exposición de datos | QA / Seguridad | Revisiones, pruebas negativas, verificación de endpoints | Cambios sensibles y pre-release |
| **Rendimiento básico** | Detectar degradaciones evidentes en operaciones clave | QA / Dev | Tiempos de respuesta percibidos, consultas/listados, carga de imágenes | Pre-release / bajo demanda |
| **Usabilidad y accesibilidad** | Validar claridad, feedback, navegación y accesibilidad básica | QA / UX | Heurísticas, checklist WCAG AA (muestreo) | Por cambios UI |
| **Compatibilidad** | Verificar comportamiento en navegadores objetivo | QA | Playwright (Chromium/Firefox/WebKit), validación responsive | Regresión/release |
| **UAT / Aceptación** | Confirmar valor y aceptación del negocio | PO / Usuarios clave | Escenarios de aceptación | Hito de liberación |

### 5.2 Alcance funcional de pruebas (matriz mínima)

| Módulo | Casos críticos | Resultado esperado |
|---|---|---|
| Autenticación | Registro, login email/clave, `validate-token`, logout, Google OAuth (si habilitado) | Sesión válida; 401 sin token; estado UI coherente |
| Usuarios/Perfil | `GET /api/users/me`, roles y accesos admin | Sin exposición de datos sensibles; 403 a no autorizados |
| Hoteles (público) | Listado, detalle, búsqueda (filtros, sort, paginación), persistencia de criterios | Resultados correctos y estables; manejo de vacío/errores |
| Hoteles (owner) | Crear/editar hotel, carga de imágenes (hasta 6), ownership, activar/desactivar | Persistencia correcta; imágenes en Cloudinary; control de acceso |
| Reservas y pagos | PaymentIntent, confirmación solo con pago exitoso, mis reservas, cancelación/reembolso según reglas | No se crean reservas inválidas; estados y totales consistentes |
| Gestión de reservas | Listados por usuario/hotel, cambios de estado/pago, eliminación según permisos | Reglas de negocio y authz cumplidas |
| Reviews / Insights | Crear/listar reviews (restricciones), dashboards/forecast/stats | Agregados y métricas coherentes; endpoints estables |
| Admin / IA (si habilitado) | Acceso `/admin/*`, acciones administrativas, sugerencias IA (borrador sin persistencia automática) | Controles de rol; sin fugas de secretos; flujo seguro |
| Salud / Documentación | `/api/health`, `/api/health/detailed` (JWT), `/api-docs` | Liveness público mínimo; detalle protegido; docs accesibles |

### 5.3 Datos y gestión de entorno de pruebas

| Elemento | Definición |
|---|---|
| **Entornos** | Local (dev), Staging/Preprod (recomendado, paridad con prod), Producción (smoke/monitoreo) |
| **Base de datos de prueba** | BD aislada; **no** usar BD productiva para pruebas destructivas |
| **Datos semilla** | `cd hotel-booking-backend && npm run seed` (destructivo: limpia y resembra colecciones). Usuarios seed: `test@user.com` (admin), `owner@hotel.com`, `guest@user.com`, password `12345678` |
| **Fixtures** | `data/test-users.json`, `data/test-hotel.json`, `data/hotel_images/` para apoyo a E2E/manual |
| **Credenciales externas** | Stripe en modo test (`sk_test`, `pk_test`), Cloudinary de prueba, Google OAuth de prueba (si aplica) |
| **Configuración crítica** | Backend: `MONGODB_CONNECTION_STRING`, `JWT_SECRET_KEY`, `CLOUDINARY_*`, `STRIPE_API_KEY`, `FRONTEND_URL`; Frontend: `VITE_API_BASE_URL`, `VITE_STRIPE_PUB_KEY` |

### 5.4 Criterios de entrada, salida, suspensión y reanudación

| Criterio | Condición |
|---|---|
| **Entrada** | Alcance definido; entorno disponible; variables configuradas; build exitoso; datos de prueba preparados; versión identificada (commit/tag) |
| **Salida** | Pruebas planificadas ejecutadas; sin defectos abiertos Bloqueantes/Altos; evidencias registradas; smoke post-deploy OK; aprobación de liberación |
| **Suspensión** | Entorno inestable, fallos de build bloqueantes, datos corruptos, indisponibilidad crítica de dependencias (BD/Stripe/Cloudinary) |
| **Reanudación** | Causa de suspensión resuelta, entorno restablecido, smoke básico exitoso y plan de pruebas actualizado |

---

## 6. Cronograma de aseguramiento de calidad

> **Supuesto:** cronograma relativo a partir de la fecha de inicio (_kickoff_). Ajustar fechas, duración y responsables según capacidad y alcance real.

### 6.1 Hitos y fases

| Fase | Periodo (relativo) | Actividades principales | Responsable(s) | Entregables |
|---|---|---|---|---|
| **F1. Planificación y preparación** | Semana 1 | Revisión de alcance/requisitos, definición de entorno y datos, configuración de herramientas, preparación de casos smoke y críticos | QA Lead, Dev, DevOps | Plan aprobado, checklist de entorno, casos iniciales |
| **F2. Verificación base y smoke** | Semana 1–2 | Build/lint, verificación de configuración, smoke API/UI, validación de seed y flujos básicos de auth/búsqueda | QA, Dev | Reporte de smoke, defectos iniciales |
| **F3. Pruebas funcionales e integración** | Semana 2–3 | Ejecución de matriz funcional (roles guest/owner/admin), integraciones Stripe/Cloudinary/OAuth, pruebas de API | QA, Backend, Frontend | Resultados de ejecución, evidencias, bugs registrados |
| **F4. E2E y regresión** | Semana 3–4 | Ejecución/ajuste de Playwright, regresión de flujos críticos, re-test de correcciones | QA | Reporte E2E/regresión, estado de defectos |
| **F5. Seguridad básica, no funcionales y UAT** | Semana 4 | Pruebas negativas/authz, revisión de exposición/configuración, accesibilidad/compatibilidad básica, UAT | QA, Seguridad, PO | Informe de hallazgos, acta UAT |
| **F6. Cierre y liberación** | Semana 4 | Consolidación de evidencias, verificación de criterios de salida, smoke pre/post-release, lecciones aprendidas | QA Lead, DevOps, PO | Informe final de calidad, recomendación go/no-go |

### 6.2 Calendario semanal (ejemplo)

| Actividad | S1 | S2 | S3 | S4 |
|---|:--:|:--:|:--:|:--:|
| Planificación, entorno y datos | X |  |  |  |
| Build, lint y smoke | X | X | X | X |
| Pruebas funcionales/API |  | X | X |  |
| Integraciones externas |  | X | X |  |
| E2E y regresión |  |  | X | X |
| Seguridad básica y no funcionales |  |  |  | X |
| UAT y cierre de calidad |  |  |  | X |

### 6.3 Frecuencia recomendada (operación continua)
- **Por cambio/PR:** build + lint + smoke dirigido.
- **Diaria/Nightly (si hay CI):** suite E2E y regresión priorizada.
- **Por release:** ejecución completa de fases F3–F6 según alcance.

---

## 7. Análisis de riesgos

### 7.1 Escala de valoración
- **Probabilidad:** Alta (3) / Media (2) / Baja (1)
- **Impacto:** Alto (3) / Medio (2) / Bajo (1)
- **Nivel de riesgo:** `Probabilidad x Impacto` → **Alto (6–9)**, **Medio (3–4)**, **Bajo (1–2)**

### 7.2 Matriz de riesgos

| ID | Riesgo | Categoría | Prob. | Impacto | Nivel | Mitigación | Contingencia | Responsable | Indicador |
|---|---|---|:---:|:---:|:---:|---|---|---|---|
| R-01 | Variables de entorno faltantes o incorrectas impiden arranque/conexiones | Configuración | 3 | 3 | 9 | Checklist de `.env`, validación fail-fast, documentación actualizada | Restaurar env desde gestor seguro; rollback de config | DevOps/Backend | Fallos de arranque, errores 5xx |
| R-02 | Ejecución de `seed` en BD incorrecta provoca pérdida de datos | Datos | 2 | 3 | 6 | Restringir uso a BD de prueba, confirmación previa, backups | Restaurar backup/snapshot de BD | Backend/QA | Cambios masivos inesperados en colecciones |
| R-03 | Falla de integración con Stripe (claves/modo/red) bloquea pagos/reembolsos | Integración | 2 | 3 | 6 | Usar claves test correctas, pruebas con tarjetas de prueba, manejo de errores | Reintento, deshabilitar flujo de pago temporalmente, soporte manual | Backend/QA | PaymentIntent fallidos, errores en booking/cancel |
| R-04 | Falla de Cloudinary afecta carga/visualización de imágenes | Integración | 2 | 2 | 4 | Validar credenciales/cuotas, pruebas de subida, fallback UI | Reintento/carga diferida; imágenes placeholder | Backend/Frontend | Errores de upload, URLs inválidas |
| R-05 | Defectos de autenticación/autorización (JWT, roles, ownership) | Seguridad | 2 | 3 | 6 | Pruebas negativas (401/403), revisión de middleware/rutas, pruebas por rol | Revocar sesiones, hotfix y re-test inmediato | Backend/QA | Accesos indebidos, incidencias de seguridad |
| R-06 | Exposición de información sensible en endpoints públicos/logs | Seguridad | 2 | 3 | 6 | Revisar respuestas públicas, hardening de health/endpoints, política de logs | Restringir endpoint, rotar secretos si aplica | Backend/Seguridad | Hallazgos de revisión, reportes externos |
| R-07 | Desalineación FE/BE o de `shared/types` causa errores en runtime/build | Calidad técnica | 2 | 2 | 4 | Builds en CI/local, revisión de contratos, tipado compartido | Hotfix de contrato y redeploy coordinado | Frontend/Backend | Errores TS, 4xx/5xx por payload |
| R-08 | CORS/OAuth mal configurado impide login o llamadas cross-origin | Configuración | 2 | 3 | 6 | Validar `FRONTEND_URL/BACKEND_URL` y redirect URIs por entorno | Ajuste de configuración y smoke inmediato | DevOps/Backend | Errores CORS, fallos de callback OAuth |
| R-09 | Pruebas E2E inestables (flakiness) reducen confianza en regresión | Pruebas | 3 | 2 | 6 | Estabilizar datos/selectores/esperas, aislar entorno, retries controlados | Re-ejecución dirigida, ejecución manual de respaldo | QA | Variabilidad de resultados entre corridas |
| R-10 | Ausencia de CI automatizado retrasa detección de regresiones | Proceso | 3 | 2 | 6 | Definir pipeline mínimo (build/lint/test), ejecución local obligatoria | Checklist manual pre-release reforzado | Liderazgo técnico | Defectos detectados tarde |
| R-11 | Inconsistencias en reservas/reembolsos/contadores por fallos parciales | Negocio/Datos | 2 | 3 | 6 | Pruebas de integración y casos límite, verificación de estados/totales | Reconciliación manual en BD/Stripe, corrección de datos | Backend/QA | Estados divergentes, reclamos de usuarios |
| R-12 | Dependencias de terceros o vulnerabilidades en paquetes | Seguridad/Mantenimiento | 2 | 2 | 4 | Actualizaciones controladas, escaneo periódico, revisión de advisories | Parchar/actualizar versión, mitigación temporal | Dev/Seguridad | Alertas de dependencias, CVEs |

### 7.3 Seguimiento y revisión de riesgos
- Revisión de riesgos: **al inicio de cada iteración/release** y ante incidentes relevantes.
- Escalamiento: riesgos **Altos (6–9)** requieren plan de acción y seguimiento explícito.
- Registro: mantener historial de estado (abierto, mitigado, aceptado, cerrado).

---

## 8. Gestión de defectos

### 8.1 Flujo de gestión
1. **Detección** (QA/Dev/Usuario) → 2. **Registro** → 3. **Triaje** (severidad/prioridad) → 4. **Asignación** → 5. **Corrección** → 6. **Verificación (re-test)** → 7. **Cierre** / Reapertura.

### 8.2 Clasificación
| Severidad | Descripción | Ejemplo |
|---|---|---|
| **Bloqueante** | Impide operación crítica o liberación | No se puede autenticar, no se completa pago |
| **Alta** | Funcionalidad clave degradada sin workaround razonable | Cancelación no reembolsa cuando debe |
| **Media** | Funcionalidad afectada con workaround | Filtro de búsqueda inconsistente en caso específico |
| **Baja** | Defecto menor/cosmético | Texto o alineación UI |

| Prioridad | Criterio |
|---|---|
| **P1** | Resolver de inmediato (hotfix/release blocker) |
| **P2** | Resolver en la iteración/release actual |
| **P3** | Planificar en siguiente iteración |
| **P4** | Backlog/mejora |

### 8.3 Información mínima del reporte
- ID, título, módulo, entorno, versión/commit
- Pasos para reproducir, resultado esperado vs actual
- Evidencia (capturas, video, logs, request/response)
- Severidad, prioridad, estado, responsable

---

## 9. Métricas e indicadores de calidad

| Métrica | Definición | Objetivo/Umbral (propuesto) | Frecuencia |
|---|---|---|---|
| Tasa de éxito de build/lint | % ejecuciones exitosas | ≥ 98% | Por cambio / diario |
| Tasa de aprobación de smoke | % smoke tests pasados tras deploy | 100% en críticos | Por deploy |
| Densidad de defectos | Defectos / módulo o / iteración | Tendencia decreciente | Por iteración |
| Defectos abiertos por severidad | Conteo por Bloq/Alta/Media/Baja | 0 Bloq/Alta al liberar | Semanal / release |
| Tiempo medio de corrección (MTTR) | Tiempo desde reporte a cierre | Según severidad (definir SLA) | Por iteración |
| Cobertura E2E de flujos críticos | % flujos críticos cubiertos | ≥ 80% (objetivo progresivo) | Por release |
| Tasa de flaky tests | % pruebas inestables en E2E | < 5% | Semanal |
| Reaperturas de defectos | % defectos reabiertos | < 10% | Por iteración |

---

## 10. Gestión de configuración, herramientas y evidencias

### 10.1 Herramientas

| Propósito | Herramienta / Comando |
|---|---|
| Control de versiones | Git |
| Backend build/run | Node.js, npm, TypeScript, Nodemon (`npm run dev/build/start`) |
| Frontend build/run/lint | Vite, TypeScript, ESLint (`npm run dev/build/lint`) |
| Pruebas E2E | Playwright (`e2e-tests`, `npx playwright test`) |
| Documentación/contratos API | Swagger UI (`/api-docs`) |
| Integraciones | MongoDB/Mongoose, Stripe, Cloudinary, Google OAuth |
| Contenedorización | Docker (backend, contexto raíz del repo) |
| Despliegue | Vercel (frontend), Coolify/VPS (backend) — según entorno |

### 10.2 Control de configuración y evidencias
- **Configuración:** versionar plantillas (`.env.example`, `.env.local.example`), no versionar secretos reales.
- **Baselines:** identificar versiones probadas por commit/tag y entorno.
- **Evidencias mínimas por ciclo:** resultados de build/lint, reporte de smoke, matriz de ejecución, reporte E2E, listado de defectos y resolución, informe final de QA.
- **Retención:** almacenar evidencias en el repositorio de documentación/herramienta de QA definida por el equipo.

---

## 11. Revisiones, auditorías y mejora continua

| Actividad | Frecuencia | Participantes | Salida |
|---|---|---|---|
| Revisión del plan de QA | Por release o cambio mayor | QA Lead, Tech Lead, PO | Plan actualizado |
| Revisión de riesgos | Inicio de iteración y ante incidentes | Equipo | Matriz de riesgos actualizada |
| Revisión de defectos (triaje) | Semanal / según demanda | QA, Dev, PO | Priorización y plan de acción |
| Retrospectiva de calidad | Fin de iteración/release | Equipo | Acciones de mejora |
| Auditoría de configuración/seguridad básica | Pre-release | DevOps, Seguridad, QA | Hallazgos y remediaciones |

---

## 12. Comunicación y reporte

| Reporte | Audiencia | Contenido | Frecuencia |
|---|---|---|---|
| Estado diario/breve de pruebas | Equipo | Avance, bloqueos, defectos críticos | Diaria (en ejecución) |
| Reporte de iteración | Equipo/PO | Casos ejecutados, resultados, defectos, riesgos | Semanal/iteración |
| Informe de liberación | PO/Stakeholders/DevOps | Cumplimiento de criterios de salida, riesgos residuales, recomendación go/no-go | Por release |
| Informe de incidentes/hallazgos de seguridad | Equipo/Seguridad | Impacto, acciones inmediatas, remediación | Bajo demanda |

---

## 13. Formación y competencias

| Necesidad | Público | Acción |
|---|---|---|
| Flujo de negocio y roles del sistema | QA/Dev nuevos | Onboarding con `README.md` y `docs/PROJECT_WALKTHROUGH.md` |
| Ejecución de pruebas E2E | QA/Dev | Guía de Playwright, datos seed, convenciones de entorno |
| Seguridad y manejo de secretos | Equipo | Revisión de `SECURITY.md`, prácticas de env/config |
| Proceso AQMS del repositorio (si aplica) | Equipo | Revisión de `.agile-v/` (REQs, gates, evidencias) |

---

## 14. Dependencias y proveedores (terceros)

| Servicio/Proveedor | Uso | Riesgo asociado | Control |
|---|---|---|---|
| MongoDB (Atlas/local) | Persistencia | Disponibilidad, pérdida de datos | BD de prueba, backups, seed controlado |
| Stripe | Pagos y reembolsos | Fallos de integración, modo/clave incorrecta | Modo test, tarjetas de prueba, monitoreo de errores |
| Cloudinary | Almacenamiento de imágenes | Fallos de upload/cuotas | Credenciales de prueba, validación y fallback |
| Google OAuth | Autenticación social | Configuración de redirect/orígenes | Validación por entorno y smoke de login |
| Vercel / Coolify | Despliegue FE/BE | Configuración de entorno y disponibilidad | Smoke post-deploy, rollback |
| Proveedores LLM (opcional) | Asistente IA (borradores) | Disponibilidad, latencia, costos, límites | Feature flag, fallback/stub, sin persistencia automática |

---

## 15. Aprobación del plan

| Rol | Nombre | Firma | Fecha |
|---|---|---|---|
| Elaboró (QA) | _[Nombre]_ | _[Firma]_ | AAAA-MM-DD |
| Revisó (Tech Lead) | _[Nombre]_ | _[Firma]_ | AAAA-MM-DD |
| Aprobó (PO/Stakeholder) | _[Nombre]_ | _[Firma]_ | AAAA-MM-DD |

---

## 16. Anexos

### Anexo A — Checklist de smoke mínimo
- [ ] Backend build OK (`hotel-booking-backend`)
- [ ] Frontend build y lint OK (`hotel-booking-frontend`)
- [ ] `GET /api/health` responde estado esperado
- [ ] SPA carga en entorno objetivo
- [ ] Login y navegación básica funcionan
- [ ] Búsqueda y detalle de hotel funcionan
- [ ] Flujo crítico de reserva (según entorno/Stripe test) validado

### Anexo B — Comandos de verificación (referencia)
```bash
# Backend
cd hotel-booking-backend && npm run build

# Frontend
cd hotel-booking-frontend && npm run build && npm run lint

# E2E (con BE y FE en ejecución)
cd e2e-tests && npx playwright test
```

### Anexo C — Consideraciones para CI (recomendado)
- Pipeline por etapas: install/cache → build backend → build/lint frontend → (opcional) E2E con servicios levantados.
- Publicar artefactos: reportes Playwright, logs y evidencias de pruebas.
- Definir `baseURL`/`webServer` o variables para ejecución reproducible en CI.
