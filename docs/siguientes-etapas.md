# Siguientes etapas del proceso

| Etapa | Idea inicial |
|-------|--------------|
| 8. Tácticas | Cola y límite de ritmo, reintentos con espera, idempotencia, rate limiting del panel, JWT o sesiones con roles, caché de conteos del dashboard, backups. |
| 9. Componentes | Los módulos del diagrama 6.2. |
| 10. Diseño interno | `Controller → Caso de uso → Entidad → Repositorio → Infraestructura`. |
| 11. Tecnologías | Go, PostgreSQL, Redis, Next.js, Docker Compose, Caddy, Cloudflare, GitHub Actions. |
| 12. Implementación y despliegue | CI con pruebas, imágenes Docker, despliegue por SSH, logs y métricas básicas. |
| 13. Documentación | C4 (contexto, contenedores, componentes), ADR en `docs/arquitectura/adr/`, README de instalación. |

### Hito 1 propuesto (para trabajar con Spec Kit)

1. Estructura del repositorio, Docker Compose y migraciones.
2. Autenticación y roles (RF15).
3. Gestión de bots y webhook por bot (RF16, ADR-007).
4. Registro de contactos desde `/start` con prevención de duplicados (RF01, RF02).
5. Enlaces de ingreso con código corto y procedencia (RF03).
6. Etiquetas y asignación masiva (RF04).
7. Listado, filtros, ficha y exportación (RF05, RF13).
8. Backups y documentación de instalación.

**Criterio de hecho del hito 1:** el cliente crea un enlace, una persona inicia el bot desde él, aparece en el panel con su procedencia, y el operador la etiqueta y la ve en su ficha.
