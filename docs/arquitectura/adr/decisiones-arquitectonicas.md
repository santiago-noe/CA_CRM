# Fase 5 — Decisiones arquitectónicas (ADR)

**Pregunta guía:** ¿Cómo responderemos a los drivers?

### 5.1 Resumen

| ID | Decisión | Drivers | Estado |
|----|----------|---------|--------|
| ADR-001 | Monolito modular con dos procesos (API y Worker) | DA01, DA05, DA06, DA08 | Propuesta |
| ADR-002 | Clean Architecture / Hexagonal en el backend | DA05, DA07, AC06 | Propuesta |
| ADR-003 | Backend en Go | DA01, DA03, DA08 | Decidido (stack del desarrollador) |
| ADR-004 | PostgreSQL como fuente de verdad | DA02, DA03 | Propuesta |
| ADR-005 | Redis + worker para la cola de envíos | DA01, DA03 | Propuesta (⚠️ evaluar cola en PostgreSQL) |
| ADR-006 | Bots como entidad de primera clase (multi-bot) | DA02 | Propuesta |
| ADR-007 | Recepción por webhook con verificación de secreto | DA04, DA07 | Propuesta |
| ADR-008 | **botcrm como referencia, no como base de código** | DA05, DA06 | Propuesta (⚠️ validar en la reunión) |
| ADR-009 | Despliegue con Docker Compose en VPS del cliente, detrás de Cloudflare | DA08, AC10 | Propuesta |
| ADR-010 | Backups automáticos cifrados + prueba de restauración | AC09, RF18 | Propuesta |
| ADR-011 | Aislamiento por `workspace_id` desde el inicio (aunque haya un solo cliente) | DA02, DA05 | ⚠️ Decisión abierta (ver Anexo B) |

### 5.2 Detalle de las decisiones

#### ADR-001 — Monolito modular con dos procesos (API y Worker)
- **Contexto:** hay que recibir eventos y atender el panel de forma fluida mientras se procesan campañas largas. Un solo desarrollador mantiene todo (DA06).
- **Alternativas:** (a) monolito de un solo proceso; (b) microservicios; (c) monolito modular con un segundo proceso para trabajos en segundo plano.
- **Decisión:** (c). Un solo repositorio y base de código en Go, desplegado como dos procesos: `api` (REST + webhook) y `worker` (cola, campañas programadas, reintentos).
- **Consecuencias:** simple de operar y probar; el envío masivo no frena al panel; si más adelante hace falta, el worker se escala por separado. A cambio, hay que cuidar los límites entre módulos para que no se enreden.

#### ADR-002 — Clean Architecture / Hexagonal
- **Contexto:** la lógica del negocio (estados de contacto, segmentación, campañas) no debe depender de Telegram, de PostgreSQL ni de Redis.
- **Alternativas:** capas simples; arquitectura limpia/hexagonal.
- **Decisión:** arquitectura limpia con puertos y adaptadores (ver Fase 7).
- **Consecuencias:** se puede probar la lógica sin Telegram ni base de datos reales, y reemplazar la cola o el proveedor de mensajes sin tocar el dominio. Cuesta algo más de estructura inicial.

#### ADR-003 — Backend en Go
- **Contexto:** muchas conexiones concurrentes, trabajo en segundo plano y un VPS modesto.
- **Decisión:** Go. Buen manejo de concurrencia, binario único y bajo consumo de memoria. Es el stack que el desarrollador eligió.
- **Consecuencias:** un solo ejecutable por proceso, fácil de desplegar en Docker. Obliga a escribir más código "a mano" que en un framework de alto nivel.

#### ADR-004 — PostgreSQL
- **Contexto:** datos relacionales (contactos, etiquetas, segmentos, campañas, entregas) con filtros combinados y volumen creciente.
- **Decisión:** PostgreSQL como única fuente de verdad. Los segmentos se guardan como reglas (JSONB) y se evalúan con consultas SQL.
- **Consecuencias:** transacciones, índices y exportaciones sólidas. Hay que diseñar bien los índices (`bot_id`, `status`, fechas, etiquetas) para 100 000+ contactos.

#### ADR-005 — Cola de envíos con Redis + Worker
- **Contexto:** respetar los límites de Telegram, reintentar y programar campañas.
- **Alternativas:** (a) cola en PostgreSQL (tabla de entregas + bloqueo por filas); (b) Redis.
- **Decisión:** Redis para el control de ritmo y las colas, con **el estado de cada entrega persistido en PostgreSQL** (así no se pierde nada si Redis se reinicia).
- **Consecuencias:** mejor control de velocidad. Agrega un servicio más al VPS. ⚠️ Si se quiere minimizar servicios, la opción (a) es válida para la escala inicial; se decide al comenzar el módulo de campañas.

#### ADR-006 — Bots como entidad de primera clase
- **Contexto:** el cliente quiere varios bots con audiencias separadas (RF16, RF17).
- **Decisión:** tabla `bots` y `bot_id` en contactos, etiquetas, segmentos, enlaces, campañas y entregas. Un contacto es único por `(bot_id, telegram_user_id)`; si una misma persona usa dos bots, se registra en ambos con vínculo opcional por `telegram_user_id`, sin mezclar audiencias.
- **Consecuencias:** nuevos bots se agregan por configuración. ⚠️ Confirmar con el cliente si un mismo Telegram ID en dos bots debe verse como "la misma persona" en alguna vista.

#### ADR-007 — Webhook con secreto
- **Decisión:** cada bot recibe updates por webhook HTTPS con `secret_token` verificado; se responde rápido y el procesamiento pesado va a la cola.
- **Consecuencias:** requiere dominio con HTTPS (Cloudflare + proxy). El procesamiento del webhook debe ser idempotente (Telegram puede reenviar).

#### ADR-008 — botcrm como referencia, no como base de código
- **Contexto:** botcrm (`Evgenykravchenko/botcrm`) es un CRM open source self-hosted para bots, con diálogos, contactos, embudos, envíos masivos y automatizaciones.
- **Alternativas:**
  1. **Forkear botcrm** y adaptarlo.
  2. **Desplegar botcrm tal cual** y conectar el bot por su SDK/Gateway.
  3. **Construir el núcleo propio** usando botcrm como referencia de diseño.
- **Decisión:** opción 3.
- **Razones:**
  - **Licencia AGPL-3.0:** si se ofrece una versión modificada a terceros por red (p. ej. a las amistades del cliente), hay que ofrecerles el código fuente. El requerimiento pide propiedad y libertad de uso (R05). ⚠️ Consultar a un abogado si se opta por fork.
  - **Stack distinto:** TypeScript/Next.js/PostgreSQL/Redis/MinIO frente a Go. Forkear obligaría a reescribir o a adoptar su stack.
  - **Alcance distinto:** botcrm está orientado a conversaciones con operador, embudos y automatizaciones. El cliente pide un CRM de **captación, etiquetas, segmentos y campañas** con trazabilidad de procedencia. Parte del alcance de botcrm sobra y parte falta (ver Anexo A).
  - **Idioma y documentación:** el README original está en ruso. ⚠️ Verificar el idioma del panel y de `docs/`.
  - **Infraestructura vigente:** su `docker-compose` usa imágenes de MinIO que ya no se descargan de Docker Hub; levantarlo hoy exige parchearlo.
  - **Continuidad (DA05):** un código propio, pequeño y documentado es más fácil de entregar que un fork de un proyecto ajeno que evoluciona aparte.
- **Consecuencias:** más trabajo inicial, pero control total. Se reutilizan ideas de botcrm: estados de conversación, idempotencia por `eventId`, verificación HMAC en webhooks, reintentos, copias cifradas y SDK.
- **Cuándo revisar la decisión:** si el cliente prioriza tener algo funcionando en pocas semanas y acepta la licencia AGPL y el stack, la opción 2 es el atajo.

#### ADR-009 — Docker Compose en VPS del cliente, detrás de Cloudflare
- **Decisión:** servicios `web`, `api`, `worker`, `postgres`, `redis` y un proxy inverso (Caddy o similar), orquestados con Docker Compose. Cloudflare delante para DNS, TLS y protección.
- **Consecuencias:** sirve para un VPS de 2 vCPU y 4 GB como mínimo (a validar con pruebas de carga). Cuentas, dominio y servidor quedan **a nombre del cliente**.

#### ADR-010 — Backups
- **Decisión:** copia diaria de PostgreSQL con `pg_dump` cifrada y guardada fuera del servidor, con retención definida y **prueba de restauración documentada**. ⚠️ Definir frecuencia y retención con el cliente.

#### ADR-011 — Aislamiento por `workspace_id` ⚠️
- **Contexto:** el cliente preguntó si podrá dar el CRM a amistades con la misma necesidad.
- **Alternativas:** (a) una instalación por negocio; (b) un solo sistema multi-negocio con `workspace_id` en todas las tablas.
- **Propuesta:** **diseñar con `workspace_id` desde el inicio**, aunque en el primer despliegue haya uno solo. Cuesta poco ahora y evita una migración dolorosa después. La decisión comercial (instalaciones separadas o sistema compartido, quién paga, quién da soporte) se toma aparte.
