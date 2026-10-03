# Fase 3 — Atributos de calidad

**Pregunta guía:** ¿Qué características importantes debe tener el sistema?

| ID | Atributo | Escenario de calidad |
|----|----------|----------------------|
| AC01 | **Rendimiento** | Al enviar una campaña de decenas de miles de mensajes, el panel sigue respondiendo y los nuevos contactos se siguen registrando. |
| AC02 | **Escalabilidad** | La base puede crecer de decenas de miles a 100 000+ contactos y de 1 a varios bots sin rediseñar el sistema. |
| AC03 | **Seguridad** | Los tokens de bots, los datos de contactos y los accesos al panel están protegidos frente a accesos no autorizados; cada rol ve solo lo que le corresponde. |
| AC04 | **Disponibilidad** | La recepción de contactos por webhook funciona de forma continua; si falla un reinicio, no se pierden eventos ni envíos pendientes. |
| AC05 | **Confiabilidad de entrega** | Un envío no se duplica ni se pierde ante caídas o reintentos (idempotencia). |
| AC06 | **Mantenibilidad** | Un cambio en un módulo (p. ej. segmentos) no obliga a modificar otros; otro desarrollador puede continuar el proyecto con la documentación. |
| AC07 | **Observabilidad** | Se pueden revisar logs y métricas básicas de campañas, cola y errores. |
| AC08 | **Usabilidad** | Un operador sin conocimientos técnicos crea una etiqueta, un enlace o una campaña con pocos pasos. |
| AC09 | **Recuperabilidad** | Ante pérdida de datos se restaura desde un backup con un procedimiento documentado y probado. |
| AC10 | **Portabilidad** | El sistema se puede mover a otro servidor o proveedor sin cambios de código (todo en contenedores). |
