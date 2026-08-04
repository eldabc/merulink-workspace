# Merulink Demo

Separación de entornos para Merulink con **dos entornos Docker aislados** (DEV y PROD) que pueden correr **al mismo tiempo** sin chocar entre sí.

---

## 🏗️ Arquitectura

|              | **PROD**                                          | **DEV** |
| ---|---|---  |
| *Frontend*   | `http://localhost:8082` (Nginx, imagen compilada) | `http://localhost:8083` (Vite, hot reload) |
| *Backend*    | Laravel (imagen compilada)                        | Laravel (código montado, cambios instantáneos) |
| *BD externa* | `localhost:5434`                                  | `localhost:5435`            |
| *Volumen BD* | `postgres_data_demo` (datos actuales)             | `postgres_data_dev` (nueva) |
| *Proyecto Docker* | `merulink-demo` (por defecto)                | `merulink-demo-dev`         |

Ambos entornos están **aislados**: proyectos, volúmenes, redes y puertos separados.

---

## 📄 Archivos clave

| Archivo              | Rol |
|  ---|---|---         |
| `.env.prod`          | Variables del entorno de **producción** (BD `merulink`, puertos 5434/8082) |
| `.env.dev`           | Variables del entorno de **desarrollo** (BD `merulink_dev`, puertos 5435/8083) |
| `docker-compose.yml` | **Base** env-driven, compartido por ambos entornos (sin `container_name` ni red fija) |
| `docker-compose.override.yml` | **Solo DEV**: monta el código local + Vite dev server (HMR) |
| `.env` | Solo **fallback** si no pasas `--env-file` |

---

## 🛠️ Comandos del día a día

### Levantar PROD (imágenes compiladas, cambios solo al reconstruir)

```bash
docker compose -f docker-compose.yml --env-file .env.prod up -d
```

### Levantar DEV (código montado + hot reload)

```bash
docker compose -p merulink-demo-dev -f docker-compose.yml -f docker-compose.override.yml --env-file .env.dev up -d
```

### Detener cada entorno

Mismo comando de arriba pero terminando con `down` en vez de `up -d`.

### Reconstruir imágenes (después de cambiar `start.sh` o `Dockerfile`)

```bash
docker compose -f docker-compose.yml --env-file .env.prod build
# y recrear:
docker compose -f docker-compose.yml --env-file .env.prod up -d --force-recreate backend queue
```

---

## ⚠️ Notas

1. **IP del backend**: al recrear el backend, su IP cambia. El frontend (nginx) cachea la IP del upstream `backend`, así que hay que reiniciar el frontend del proyecto tras recrear el backend.

2. **Sin `container_name`**: los contenedores los nombra Docker por proyecto (`merulink-demo-db-1`, `merulink-demo-dev-db-1`...). Esto permite que ambos entornos corran a la vez.

3. **Variables del `db`**: el servicio `db` lee `POSTGRES_*` del `--env-file` (`.env.prod` / `.env.dev`). El `env_file` de `backend`/`queue` usa `${ENV_FILE}` para inyectar el `.env` correcto de cada entorno.

4. **`merulink-back/.env` no llega a la imagen**: el `.dockerignore` lo excluye del build. El contenedor solo ve lo que inyecta `docker-compose` (`env_file`). Por eso algunas variables van en `.env.prod` y `.env.dev`. Los .env dentro de los directorios back/front sirven para ejecución sin docker.

5. **Ports `!override` en DEV**: el frontend DEV usa `ports: !override` en el override para **reemplazar** el puerto del base (evita conflicto entre `8083:80` del nginx y `8083:5173` de Vite).

---
