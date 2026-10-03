# Fase 1 — Necesidad del negocio y actores

**Pregunta guía:** ¿Qué problema queremos resolver?

### 1.1 Problema

El cliente capta personas por Telegram (grupos, campañas, programas de capacitación) pero no tiene una base de contactos propia: no sabe de qué enlace llegó cada persona, no puede segmentarlas por interés y no puede enviarles campañas masivas de forma ordenada y controlada.

### 1.2 Objetivos del negocio

| ID | Objetivo |
|----|----------|
| OB01 | Construir una base de contactos **propia** a partir de quienes inician el bot. |
| OB02 | Saber automáticamente **de dónde llegó** cada persona (grupo, campaña, programa, origen). |
| OB03 | Clasificar contactos por **intereses** (etiquetas) y **segmentos** reutilizables. |
| OB04 | Enviar **campañas masivas y programadas** respetando los límites de Telegram. |
| OB05 | Mantener **control de infraestructura, código y datos** (propiedad del cliente). |
| OB06 | Operar **varios bots** (marcas, unidades, segmentos) desde un único CRM sin mezclar audiencias. |
| OB07 | Quedar listo para **futuras integraciones por API** y para crecer a 100 000+ contactos. |

### 1.3 Resultado esperado

Una plataforma administrable por el equipo del cliente, con su propio backend, base de datos y panel web, que reemplace el trabajo manual de captar, clasificar y escribir a los contactos.

### 1.4 Actores

| Actor | Tipo | ¿Qué necesita hacer? |
|-------|------|----------------------|
| Administrador | Humano | Configurar bots, usuarios, etiquetas, enlaces, campañas; ver todo. |
| Operador | Humano | Consultar contactos, etiquetar, preparar/enviar campañas según permisos. |
| Usuario de Telegram | Humano (externo) | Inicia el bot desde un enlace; recibe mensajes. |
| Telegram Bot API | Sistema externo | Entrega eventos (webhook) y envía mensajes. |
| Cloudflare | Sistema externo | DNS, TLS y protección perimetral. |
| Sistemas del cliente (futuro) | Sistema externo | Consumen la API del CRM. |
