# Fase 4 — Drivers arquitectónicos

**Pregunta guía:** ¿Qué condiciona las decisiones de arquitectura?

> Nota (Guía 03): no todo requisito se convierte en driver. Solo los que cambian de forma significativa cómo diseñamos el sistema.

| ID | Driver | Origen | ¿Por qué influye en la arquitectura? |
|----|--------|--------|---------------------------------------|
| DA01 | Los envíos masivos deben ser **asíncronos**, con control de velocidad, `retry_after` y reintentos. | RF10, AC01, AC05 | Obliga a separar la recepción/panel del procesamiento de envíos: **cola + worker** y estado persistente de cada entrega. |
| DA02 | **Múltiples bots** con datos diferenciados desde el diseño. | RF16, RF17 | El bot es una entidad de primera clase: casi toda tabla lleva `bot_id`. Afecta el modelo de datos, permisos y campañas. |
| DA03 | **Escala a 100 000+ contactos** sin reconstruir. | RNF03, AC02 | Condiciona la base de datos (índices, paginación), el cálculo de segmentos y el procesamiento por lotes. |
| DA04 | **Seguridad de tokens y datos** + roles. | RNF01, RF15, AC03 | Autenticación, autorización por rol, cifrado/gestión de secretos, auditoría. |
| DA05 | **Propiedad y continuidad** (código, accesos, otro desarrollador). | R05, RF18, AC06 | Favorece un diseño simple, documentado, portable, sin dependencias propietarias y con límites de módulo claros. |
| DA06 | **Un solo desarrollador, tiempo acotado** (Claude Code + Spec Kit). | R07 | Favorece un **monolito modular** frente a microservicios, y especificaciones claras por módulo. |
| DA07 | **Dependencia de la API de Telegram** (límites, bloqueos, formatos de mensaje). | R03, RF08, RF11 | Se aísla Telegram detrás de un adaptador para poder probarlo y adaptarlo a cambios. |
| DA08 | **Control de infraestructura** del cliente, posiblemente un VPS modesto. | R01, R09 | Limita recursos: pocos servicios, todo en Docker Compose, sin clústeres. |
| DA09 | **Integraciones futuras por API.** | OB07 | Se necesita una API REST bien definida y versionada, separada del panel. |
