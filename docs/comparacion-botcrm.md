# Comparación con botcrm

> ⚠️ Fuente: **solo el README** (no se revisó el código). Cada fila "Por verificar" se confirma clonando el repo y mirando `docs/`, `services/api` y `infra/`.

| Necesidad del cliente | Lo que indica botcrm | Acción |
|-----------------------|----------------------|--------|
| Registrar contactos desde `/start` | Gestiona contactos y recibe eventos por Gateway o SDK (`incoming`). | Reusar la idea; verificar campos de procedencia. |
| Enlaces con área/programa/origen/campaña (RF03) | No se menciona de forma explícita. | **Construir** (módulo propio). |
| Etiquetas y segmentos (RF04, RF06) | Menciona contactos, variables y embudos. | Por verificar; probablemente construir segmentos guardados. |
| Campañas masivas, programación y cola (RF07 a RF10) | Menciona envíos masivos y, en el worker, colas, entregas, reintentos y retención. | Estudiar su diseño del worker y de los reintentos. |
| Bloqueo del bot y reactivación (RF11) | No se menciona de forma explícita. | Por verificar. |
| Multi-bot (RF16) | Maneja `workspaceId` y `botId`. | Estudiar su modelo; coincide con ADR-006 y ADR-011. |
| Roles Administrador/Operador (RF15) | Menciona autenticación y "propietario"; operadores en diálogos. | Por verificar permisos. |
| Dashboard y estadísticas (RF12, RF14) | Menciona analítica. | Por verificar. |
| Exportar a CSV/Excel (RF05) | No se menciona. | Construir. |
| Backups cifrados (RF18, RNF02) | Incluye backup cifrado de PostgreSQL y MinIO. | Reusar el enfoque. |
| Despliegue en VPS con HTTPS | Caddy, certificados automáticos, comandos `prod:*`. | Reusar la idea de `prod:init`/`prod:check`. |
| Licencia | AGPL-3.0. | Ver ADR-008. |
| Stack | TypeScript, Next.js, PostgreSQL, Redis, MinIO. | Distinto al elegido (Go). |
| Idioma | README original en ruso. | Verificar interfaz y documentación. |
| Infraestructura hoy | `infra/docker-compose.yml` usa imágenes `minio/minio` y `minio/mc` que ya no se pueden descargar de Docker Hub. | Parchear solo si se usa para pruebas. |

**Qué copiar como ideas (no código):** estados de conversación `BOT/HUMANO/PAUSADO`, idempotencia por `eventId`, firma HMAC en webhooks, retención y reintentos en el worker, scripts de verificación de producción, backups cifrados, SDK de cliente.
