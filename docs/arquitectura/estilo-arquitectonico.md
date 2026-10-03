# Fase 6 — Estilo arquitectónico

**Pregunta guía:** ¿Cómo estructuramos globalmente el sistema?

**Estilo elegido:** **Monolito modular + arquitectura en capas**, desplegado como dos procesos (API y Worker) más un panel web.
*(Capas = organización lógica; monolito = unidad de despliegue; pueden coexistir.)*

### 6.1 Diagrama de contexto y despliegue

```mermaid
flowchart TD
    subgraph ACTORES["ACTORES"]
        Admin["Administrador"]
        Oper["Operador"]
        Usuario["Usuario de Telegram"]
    end

    subgraph EXTERNOS["SISTEMAS EXTERNOS"]
        TG["Telegram Bot API"]
        CF["Cloudflare - DNS, TLS, proteccion"]
    end

    subgraph VPS["VPS del cliente - Docker Compose"]
        Proxy["Proxy inverso"]
        Web["Panel web - Next.js"]
        subgraph MONO["Monolito modular en Go"]
            API["Proceso API - REST y webhook"]
            Worker["Proceso Worker - colas y campanas"]
        end
        PG[("PostgreSQL")]
        RD[("Redis")]
    end

    Admin --> CF
    Oper --> CF
    CF --> Proxy
    Proxy --> Web
    Web -->|REST JSON| API
    Usuario --> TG
    TG -->|webhook HTTPS| CF
    API --> PG
    API --> RD
    Worker --> RD
    Worker --> PG
    Worker -->|sendMessage| TG
```

### 6.2 Módulos del monolito

```mermaid
flowchart LR
    subgraph NUCLEO["Modulos del monolito"]
        Ident["Identidad y roles"]
        Bots["Bots"]
        Contactos["Contactos"]
        Enlaces["Enlaces de ingreso"]
        Etiquetas["Etiquetas"]
        Segmentos["Segmentos"]
        Campanas["Campanas"]
        Entregas["Entregas y cola"]
        Panel["Estadisticas y dashboard"]
    end

    Contactos --> Bots
    Enlaces --> Bots
    Etiquetas --> Contactos
    Segmentos --> Contactos
    Segmentos --> Etiquetas
    Campanas --> Segmentos
    Campanas --> Bots
    Entregas --> Campanas
    Entregas --> Contactos
    Panel --> Campanas
    Panel --> Contactos
    Ident -.-> Bots
```

**Reglas de los módulos** (inspiradas en la Guía 03):
1. Cada módulo tiene su carpeta y su propio caso de uso.
2. Un módulo **no lee las tablas de otro**; se comunica llamando a su servicio o puerto.
3. Las dependencias van de arriba hacia abajo (campañas depende de segmentos, no al revés).
4. Todo se despliega como una sola base de código; el worker reutiliza los mismos módulos.

### 6.3 Modelo de datos inicial (borrador a refinar con Spec Kit)

| Tabla | Contenido clave |
|-------|-----------------|
| `workspaces` | Negocio o cliente que usa el sistema (⚠️ ver ADR-011). |
| `users` | Email, hash de contraseña, rol (`ADMIN`, `OPERADOR`), permisos. |
| `bots` | Nombre, token cifrado, secreto de webhook, estado, `workspace_id`. |
| `contacts` | `bot_id`, `telegram_user_id`, nombre, apellido, username, estado, fecha de registro. Único `(bot_id, telegram_user_id)`. |
| `entry_links` | Código corto, área, programa, origen, grupo/campaña, `bot_id`. |
| `contact_entries` | Cada ingreso del contacto: enlace usado y fecha (historial de procedencia). |
| `tags` / `contact_tags` | Etiquetas por bot y su asignación a contactos. |
| `segments` | Reglas guardadas (JSONB) por bot. |
| `campaigns` | Bot, destinatarios (segmento/etiqueta/selección), contenido, programación, estado. |
| `deliveries` | Una fila por contacto y campaña: estado, intentos, error, fechas. |
| `contact_status_history` | Cambios de estado con motivo y fecha. |
| `audit_log` | Quién hizo qué y cuándo. |
