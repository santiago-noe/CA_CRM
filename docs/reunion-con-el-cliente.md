# Para la reunión con el cliente

### B.1 Decisiones y datos que hay que cerrar

1. **Servidor:** tipo de plan (¿VPS con root, SSH y Docker?), RAM y dominio. Cuentas del dominio, servidor y Cloudflare **a nombre del cliente**.
2. **Bots:** ¿ya existen? ¿Cuántos al inicio? El token y el bot deben quedar a nombre del cliente.
3. **Multi-negocio (ADR-011):** ¿instalaciones separadas por cada amistad o un solo sistema? ¿Quién paga, quién da soporte, quién es dueño del código?
4. **Hito 1:** ¿se acepta empezar con registro de contactos, enlaces, etiquetas, listado/ficha, usuarios y modelo multi-bot, dejando campañas para el hito 2?
5. **Volumen real:** contactos actuales, crecimiento y tamaño típico de una campaña.
6. **Contenido de campañas:** ¿qué formatos usan hoy (PDF, imágenes, botones)?
7. **Datos personales:** qué se guarda, consentimiento y política de privacidad. ⚠️ Conviene revisar la normativa peruana de protección de datos personales (Ley N.° 29733) con asesoría adecuada.
8. **Backups:** frecuencia y dónde se guardan fuera del servidor.
9. **Acuerdos:** por escrito, qué cubre cada parte (servidor, suscripción de Claude, apoyo en prácticas, reconocimiento económico, trabajos posteriores).

### B.2 Sugerencias para plantear

- Empezar por un **hito 1 pequeño y demostrable** y mostrar avances cada semana.
- **Diseñar multi-bot y `workspace_id` desde el inicio**: es barato ahora y caro después.
- Dejar claro el **límite de Telegram**: solo se escribe a quienes iniciaron el bot.
- Acordar un **procedimiento de cambios**: lo nuevo que pida el cliente entra al siguiente hito, no al actual.
