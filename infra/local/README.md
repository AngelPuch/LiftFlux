# Entorno local

`compose.yml` prepara únicamente la instancia PostgreSQL de `auth-service`. Los otros cinco servicios todavía no tienen aplicación ni base configurada; cada uno recibirá su propia instancia cuando se implemente. Este Compose es para desarrollo local, no describe una topología de producción ni publica la base a Internet.

1. Copia `.env.example` a `.env` en este directorio y define `AUTH_DB_PASSWORD` con un valor local propio. `.env` está excluido de Git.
2. Con Docker Engine y Compose disponibles, ejecuta `docker compose --env-file infra/local/.env -f infra/local/compose.yml up -d auth-db` desde la raíz del repositorio.
3. Comprueba el estado con `docker compose --env-file infra/local/.env -f infra/local/compose.yml ps`.
4. Configura `services/auth-service/.env` con `DATABASE_URL` para `localhost:5433` y los valores de Supabase del entorno de desarrollo. El servicio inicial aún no usa esa configuración ni tiene migraciones.

No reutilices la contraseña de ejemplo en staging o producción. En esos entornos se fijarán versiones exactas de imágenes, TLS, secretos montados, respaldos y redes privadas conforme a los ADR. No ejecutes `down -v` si necesitas conservar datos locales.
