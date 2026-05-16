# technical-project-analysis

# Full-stack FastAPI · React · MongoDB — Generador de proyectos

**Plantilla Cookiecutter** que genera el esqueleto de un stack **FARM** (FastAPI, React, MongoDB) orientado a producción: API backend, frontend web, persistencia, trabajos en segundo plano, proxy inverso y estructura adecuada para CI. Acelera las **primeras semanas de desarrollo**; no es un producto cerrado ni un constructor de sitios sin código.

![Landing](img/landing.png)

---

## Qué es este repositorio

| Aspecto | Descripción |
|---------|-------------|
| **Función** | Un **generador**: ejecutas Cookiecutter, respondes las preguntas (o usas `--no-input`) y obtienes un directorio nuevo con el esqueleto completo de la aplicación. |
| **Entrega** | Una **base de partida** para aplicaciones web progresivas: flujos de autenticación, estructura de la API, Docker Compose, enrutamiento Traefik, workers Celery y documentación OpenAPI—no la lógica de tu negocio ni los textos de la interfaz. |
| **Estado** | **Experimental**: bifurcación y evolución de los generadores centrados en PostgreSQL de [tiangolo](https://github.com/tiangolo/full-stack-fastapi-postgresql) y [whythawk](https://github.com/whythawk/full-stack-fastapi-postgresql), adaptados a MongoDB y a un frontend moderno React/Next.js. |

**Lo que sigues siendo responsable de definir:** reglas de negocio, modelos más allá de la plantilla, pantallas, integraciones, endurecimiento de seguridad para producción y operación del sistema.

---

## Arquitectura en breve

- **Cliente:** Next.js, React, TypeScript; Tailwind CSS y patrones de UI habituales; autenticación consciente de rutas.
- **API:** FastAPI, enfoque OpenAPI-first; Motor + ODMantic para MongoDB; patrones CRUD compartidos.
- **Procesamiento asíncrono:** workers Celery; Flower para observabilidad.
- **Borde:** Traefik para enrutamiento y TLS (p. ej. Let’s Encrypt) en despliegues.
- **Datos:** MongoDB como almacén principal de documentos.

Los proyectos generados incluyen la configuración de Docker Compose para levantar el stack **en local tras el build** con un flujo coherente.

---

## Requisitos previos

Instala antes de generar o ampliar la plantilla:

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Python 3.11+](https://www.python.org/downloads/)
- [Cookiecutter](https://cookiecutter.readthedocs.io/en/stable/)
- [Hatch](https://hatch.pypa.io/latest/) (empaquetado del backend alineado con [Inboard](https://inboard.bws.bio/))
- [Node.js](https://nodejs.org/) (trabajo en frontend fuera de contenedores, si aplica)
- [MongoDB Compass](https://www.mongodb.com/products/tools/compass) (opcional; útil para inspeccionar datos)

---

## Inicio rápido

> Un árbol generado **no** está listo para producción hasta revisar secretos, CORS, SMTP, dominios y la guía de despliegue.

**Desde GitHub:**

```bash
cookiecutter https://github.com/mongodb-labs/full-stack-fastapi-mongodb --no-input project_name="example"
cd example
docker compose build --no-cache
docker compose up -d
```

**Desde un clon local de esta plantilla** (ruta a la carpeta que contiene `cookiecutter.json`):

```bash
cookiecutter /ruta/a/full-stack-fastapi-mongodb --no-input project_name="example"
cd example
docker compose build --no-cache
docker compose up -d
```

**Primer acceso:** el superusuario por defecto sigue el patrón `admin@<slug-del-proyecto>.com` (p. ej. `admin@example.com` con `project_name="example"`). La contraseña inicial se define en Cookiecutter (habitualmente `changethis`—**cámbiala** antes de cualquier despliegue real).

### URLs locales (entorno Compose típico)

| Servicio | URL |
|----------|-----|
| Frontend | http://localhost:3000 |
| API (v1) | http://localhost/api/v1/ |
| Swagger UI | http://localhost/docs |
| ReDoc | http://localhost/redoc |
| Flower (Celery) | http://localhost:5555 |
| Panel Traefik | http://localhost:8090 |

---

## Documentación

| Tema | Guía |
|------|------|
| Primera ejecución y visión general | [Building a generated app](./docs/getting-started.md) |
| Entorno de desarrollo | [Development and installation](./docs/development-guide.md) |
| Despliegue en producción | [Deployment for production](./docs/deployment-guide.md) |
| Autenticación y *magic links* | [Authentication and magic tokens](./docs/authentication-guide.md) |
| Tiempo real / WebSockets | [Websockets for interactive communication](./docs/websocket-guide.md) |

El proyecto generado incluye además su propio `README.md`; puedes previsualizar la plantilla aquí: [`{{cookiecutter.project_slug}}/README.md`](./{{cookiecutter.project_slug}}/README.md).

---

## Aspectos técnicos destacados

- **Autenticación:** seguridad de API orientada a JWT, renovación de tokens, flujo opcional de *magic link* con contraseña como respaldo, credenciales con hash.
- **Calidad de API:** validación y serialización con Pydantic; documentación OpenAPI interactiva desde el primer día.
- **Frontend:** formularios, gestión de estado (Redux) y patrones de componentes adecuados para apps autenticadas.
- **Plataforma:** estructura pensada para CI; imágenes Docker para backend, worker y frontend.

Las versiones de la plantilla apuntan a dependencias núcleo **compatibles con entornos tipo LTS**; **no hay compromiso de compatibilidad hacia atrás** entre releases—actualiza aplicaciones generadas de forma consciente.

---

## Contribución y soporte

Esta plantilla es **experimental**. Si algo falla o echas en falta una capacidad útil para equipos con MongoDB y FastAPI, abre un *issue* en este repositorio.

---

## Notas de versión

**Aviso:** no se garantiza compatibilidad hacia atrás entre versiones de la plantilla.

### CalVer 2024.01.10

- ODMantic sustituye el uso de Beanie.
- Correcciones de inicio de sesión TOTP; mejoras de auth a nivel de ruta; manejo de errores en el cliente de autenticación.
- Ajustes en settings y generación del pie de página; limpieza de CORS; actualización de documentación.

### CalVer 2023.11.10

- Frontend React/Redux en lugar del enfoque anterior Next/Vue.
- PostgreSQL/SQLAlchemy reemplazados por la línea MongoDB Motor + ODM; eliminados Neo4j y Alembic.
- Variables Cookiecutter para MongoDB; soporte de Pydantic 2.

Histórico *upstream*: [releases de whythawk](https://github.com/whythawk/full-stack-fastapi-postgresql/releases), [stack original](https://github.com/tiangolo/full-stack-fastapi-postgresql#release-notes).

---

## Licencia

Este proyecto se distribuye bajo la [licencia MIT](LICENSE).
