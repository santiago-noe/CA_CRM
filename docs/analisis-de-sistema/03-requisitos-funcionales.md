# Fase 2 — Requisitos funcionales y no funcionales

**Pregunta guía:** ¿Qué debe hacer el sistema y qué limitaciones existen?

### 2.1 Requisitos funcionales (RF)

Cada RF corresponde a una sección del requerimiento del cliente. **Hito** indica en qué entrega se propone construirlo (H1 = base inicial, H2 = campañas, H3 = completar). ⚠️ Los hitos son una propuesta para validar con el cliente.

| ID | Requisito funcional | HU | Sección del requerimiento | Hito |
|----|---------------------|----|---------------------------|------|
| RF01 | Registrar automáticamente al contacto al iniciar el bot (nombre, apellido, username, Telegram ID, fecha, origen, campaña, estado). | HU01 | 1 | H1 |
| RF02 | Evitar duplicados por Telegram ID; si reingresa por otro enlace, actualizar la ficha y sumar la nueva procedencia. | HU01 | 1 | H1 |
| RF03 | Crear enlaces de ingreso con área/especialidad, programa, origen y grupo/campaña; registrar la procedencia automáticamente. | HU02 | 2 | H1 |
| RF04 | Crear, editar y eliminar etiquetas; asignarlas o quitarlas a un contacto o en masa; múltiples etiquetas por contacto. | HU04 | 3 | H1 |
| RF05 | Listar contactos con búsqueda y filtros (nombre, username, ID, etiquetas, origen, programa/campaña, fecha, estado), selección masiva y exportación a CSV/Excel. | HU03 | 4 | H1 |
| RF06 | Crear segmentos combinando condiciones y guardarlos para reutilizar. | HU05 | 5 | H2 |
| RF07 | Crear campañas dirigidas a toda la base activa, una etiqueta, un segmento o contactos seleccionados. | HU06 | 6 | H2 |
| RF08 | Soportar en la campaña texto, imágenes, PDF, enlaces y botones (según lo permita la API oficial) con vista previa. | HU06 | 6 | H2 |
| RF09 | Programar el envío de una campaña para fecha y hora determinadas. | HU07 | 7 | H2 |
| RF10 | Cola de envíos con control de velocidad, espera automática ante límites de Telegram y reintentos. | HU08 | 8 | H2 |
| RF11 | Detectar bloqueo/inaccesibilidad, actualizar estado sin borrar al contacto y excluirlo de campañas; reactivarlo si vuelve a interactuar. | HU09 | 9 | H2 |
| RF12 | Estadísticas por campaña: destinatarios, enviados, errores, bloqueados, pendientes, inicio y fin. | HU10 | 10 | H2 |
| RF13 | Ficha del contacto: datos de Telegram, estado, etiquetas, origen inicial, otros ingresos, campañas recibidas, cambios de estado. | HU11 | 11 | H1 |
| RF14 | Dashboard: total histórico, activos, bloqueados/inactivos, nuevos por periodo, contactos por etiqueta, disponibles para campañas. | HU12 | 12 | H3 |
| RF15 | Usuarios con roles Administrador (acceso total) y Operador (permisos limitados configurables). | HU13 | 13 | H1 |
| RF16 | Conectar y administrar múltiples bots; contactos, etiquetas, segmentos, enlaces, campañas y estadísticas diferenciados por bot; elegir el bot al crear campaña. | HU14 | 18 | H1 (modelo) / H2 (campañas) |
| RF17 | Registrar en cada contacto con qué bot(s) interactuó, sin mezclar audiencias de distintos segmentos de forma automática. | HU14 | 18 | H1 |
| RF18 | Entregar código fuente, accesos, documentación de instalación y mecanismo de backups. | HU15 | 15, 16 | H3 |

**Estados mínimos de contacto (RF11):** `ACTIVO`, `BLOQUEO_BOT / INACTIVO`, `CUENTA_ELIMINADA_O_INACCESIBLE`, `ERROR_TEMPORAL`.

### 2.2 Requisitos no funcionales / atributos (RNF)

| ID | RNF |
|----|-----|
| RNF01 | Todo el tráfico por HTTPS; credenciales y tokens de bots almacenados de forma segura (cifrados o en variables/secretos, nunca en el código ni en logs). |
| RNF02 | Copias de seguridad automáticas de la base de datos, con frecuencia definida y procedimiento de restauración documentado. |
| RNF03 | Diseñado para decenas de miles de contactos, con crecimiento a 100 000 o más sin reconstruir. |
| RNF04 | Los envíos masivos no deben bloquear el panel ni la recepción de nuevos contactos. |
| RNF05 | El sistema debe poder ser mantenido por otro desarrollador (código ordenado, documentado, con pruebas). |
| RNF06 | Panel simple de usar: se prioriza funcionalidad y facilidad sobre gráficos complejos. |
| RNF07 | Trazabilidad: registrar quién hizo qué (auditoría básica) y el historial de cambios de estado. |

### 2.4 Límites propios de Telegram a considerar

- Un bot **solo puede escribir a quien lo haya iniciado**; no se puede "importar" números ni usernames para escribirles.
- El **payload del enlace de ingreso** (`t.me/<bot>?start=<payload>`) es corto y de caracteres limitados. Por eso el enlace lleva un **código corto** que el CRM traduce a área/programa/origen/campaña, no los datos completos. ⚠️ Verificar el largo máximo vigente en la documentación oficial.
- Telegram impone **límites de velocidad** (orden de ~1 mensaje por segundo por chat y alrededor de 30 por segundo en difusiones). ⚠️ Confirmar valores vigentes en la documentación oficial antes de dimensionar la cola.
- Telegram responde con **espera obligatoria** (`retry_after`) cuando se excede el límite, y con error de prohibido cuando el usuario bloqueó al bot. El sistema debe tratarlos como casos normales.
