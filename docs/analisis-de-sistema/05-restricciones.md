# Fase 2 — Restricciones

### 2.3 Restricciones (R)

| ID | Restricción | Origen |
|----|-------------|--------|
| R01 | Infraestructura bajo control del cliente (VPS o cloud a su nombre). | Req. 14 |
| R02 | Flujo base: Telegram Bot → Backend propio → Base de datos propia → CRM web propio. | Req. 14 |
| R03 | Usar solo la **API oficial de Telegram** y respetar sus límites. | Req. 6 y 8 |
| R04 | El Telegram ID es el identificador principal del contacto. | Req. 1 |
| R05 | La base de contactos y el código son propiedad del cliente. | Req. 15 |
| R06 | Stack elegido por el desarrollador: Go (backend), PostgreSQL, Redis (colas), Next.js (panel), Docker, Cloudflare. | Decisión propia |
| R07 | Un solo desarrollador con Claude Code y Spec Kit (desarrollo guiado por especificaciones). | Contexto del proyecto |
| R08 | Repositorio en Git/GitHub. | Curso / buena práctica |
| R09 | ⚠️ Servidor: confirmar si el plan del cliente es un **VPS con root/SSH y Docker** (un hosting compartido no sirve). | Por confirmar |
