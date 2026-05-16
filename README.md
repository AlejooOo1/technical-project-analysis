# Full-stack FastAPI · React · MongoDB — Generador de proyectos

**Plantilla Cookiecutter** que genera el esqueleto de un stack **FARM** (FastAPI, React, MongoDB) orientado a producción: API backend, frontend web, persistencia, trabajos en segundo plano, proxy inverso y estructura adecuada para CI. Acelera las **primeras semanas de desarrollo**; no es un producto cerrado ni un constructor de sitios sin código.

![Landing](img/landing.png)

---

## Índice

1. [Qué es este repositorio](#qué-es-este-repositorio)
2. [Arquitectura en breve](#arquitectura-en-breve)
3. [Requisitos previos](#requisitos-previos)
4. [Inicio rápido](#inicio-rápido)
5. [Documentación](#documentación)
6. [Aspectos técnicos destacados](#aspectos-técnicos-destacados)
7. [**Informe técnico: refactor del cliente HTTP, estructura y metodología (IA)**](#informe-técnico-refactor-del-cliente-http-estructura-y-metodología-ia)
8. [Contribución y soporte](#contribución-y-soporte)
9. [Notas de versión](#notas-de-versión)
10. [Licencia](#licencia)

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

## Informe técnico: refactor del cliente HTTP, estructura y metodología (IA)

Esta sección documenta el **análisis del refactor** aplicado al cliente del frontend (capa HTTP), la **organización del repositorio**, el **funcionamiento global** y el uso de **inteligencia artificial (Cursor)** para diseñar e implementar los cambios. Complementa las secciones anteriores sin sustituirlas.

### Breve descripción del proyecto

Es una **plantilla generada con Cookiecutter** que produce un stack **FARM**: **FastAPI** (API REST), **React/Next.js** (interfaz) y **MongoDB** (datos). Incluye autenticación (JWT, *magic link*, TOTP, recuperación de contraseña), workers **Celery**, documentación **OpenAPI**, y despliegue típico con **Docker Compose** y **Traefik**. Objetivo: una base profesional para el desarrollo; la lógica de negocio específica queda fuera de la plantilla.

### Estructura del proyecto y función de las carpetas principales

| Ruta (plantilla bajo `{{cookiecutter.project_slug}}/`) | Función |
|--------------------------------------------------------|---------|
| `backend/app/` | Aplicación FastAPI: configuración, modelos ODM, routers, dependencias, seguridad, tareas en segundo plano. |
| `backend/app/app/api/api_v1/` | API versionada (`/api/v1`): agrupa endpoints de `login`, `users`, `proxy`, `services`, etc. |
| `frontend/` | Next.js + TypeScript: páginas (`app/`), componentes, estado global. |
| `frontend/app/lib/api/` | **Cliente HTTP hacia el backend:** `core.ts` (URL), `apiClient.ts` (política única de `fetch`), `auth.ts`, `services.ts`. |
| `frontend/app/lib/slices/` | Redux (tokens, sesión, notificaciones). |
| `frontend/app/components/` | UI reutilizable (navegación, ajustes, moderación, etc.). |
| Raíz del repo (sin slug) | `cookiecutter.json`, este `README`, `docs/`, imágenes de documentación. |

La carpeta `{{cookiecutter.project_slug}}` es el **árbol que Cookiecutter copia** al generar un proyecto con nombre personalizado.

### Funcionamiento general del sistema

1. El **frontend** usa la variable `NEXT_PUBLIC_API_URL` para construir peticiones al backend bajo `/api/v1/...`.
2. **FastAPI** valida entradas/salidas con **Pydantic**, aplica seguridad según el endpoint (OAuth2 password, Bearer JWT, etc.) y persiste vía **Motor + ODMantic** en MongoDB.
3. Los flujos de autenticación devuelven tokens que el cliente almacena y envía en `Authorization: Bearer`.
4. Tras el refactor, **casi todas** las llamadas HTTP del cliente pasan por **`apiClient.ts`** (`apiJson`, `apiRequestForm`), de modo que URL, cabeceras y errores siguen **una sola política**.

### Tecnologías utilizadas y función dentro del proyecto

| Tecnología | Función en el proyecto |
|------------|-------------------------|
| **Cookiecutter** | Genera el proyecto final a partir de la plantilla. |
| **FastAPI** | API REST, validación y documentación OpenAPI. |
| **MongoDB + Motor + ODMantic** | Persistencia documental y capa ODM. |
| **Next.js, React, TypeScript** | Interfaz web y rutas. |
| **Tailwind CSS** | Estilos. |
| **Redux** | Estado global (sesión, toasts). |
| **Celery** (stack típico) | Trabajos asíncronos. |
| **Docker Compose / Traefik** | Entorno local y proxy en despliegue. |

### Diferencias: antes y después del refactor (cliente HTTP)

| Aspecto | Antes | Después |
|---------|--------|---------|
| Llamadas HTTP | `fetch` repetido en `auth.ts`, `services.ts`, etc. | Funciones centralizadas `apiJson` / `apiRequestForm` en `apiClient.ts`. |
| Errores | Inconsistentes entre módulos. | `parseJsonOrThrow`: respuestas no OK unificadas; uso de `detail` del body de FastAPI cuando existe. |
| URLs | Riesgo de typos y barras mal unidas. | `normalizeBaseUrl` / `normalizePath`; fallo explícito si falta `NEXT_PUBLIC_API_URL`. |
| Login OAuth2 | Fácil mezclar JSON con lo que el backend espera como form. | `apiRequestForm` con `application/x-www-form-urlencoded` alineado a `OAuth2PasswordRequestForm`. |
| Endpoints auxiliares | Confusión posible entre rutas (p. ej. contacto). | Router backend `prefix="/services"` alineado con `POST /services/contact` en el cliente. |

**Por qué es mejor ahora:** un solo lugar para cambiar cabeceras, errores y construcción de URL; menos bugs de integración; módulos de dominio (`apiAuth`, `apiService`) legibles como catálogo de endpoints; onboarding más rápido.

### Fragmento de código original representativo (anti-patrón)

El código previo no está versionado aquí línea a línea; el problema descrito en la plantilla es la **duplicación** de `fetch` y manejo de error. Ejemplo **ilustrativo** del patrón antiguo:

```typescript
// Ilustrativo — antes: lógica de red repetida por archivo
async function loginWithOauth(username: string, password: string) {
  const res = await fetch(`${apiCore.url}/login/oauth`, {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: new URLSearchParams({ username, password }),
  });
  if (!res.ok) throw new Error(res.statusText);
  return res.json();
}
```

### Problema identificado

- **Violación de DRY:** misma lógica de red copiada en varios módulos.
- **Errores heterogéneos:** distinto tratamiento de cuerpos JSON y de `detail` de FastAPI.
- **Contrato HTTP frágil:** rutas desalineadas con los routers del backend.
- **OAuth2:** el endpoint de login espera formulario URL-encoded; mezclarlo con JSON rompe el contrato del servidor.

### Herramienta de IA utilizada

**Cursor** (editor con asistente integrado), empleado para razonar sobre el diseño, refactorizar de forma consistente con FastAPI y mantener el tono técnico del repositorio.

### Prompt(s) utilizados

*(Completar con los textos exactos del chat en Cursor si el entregable lo requiere.)*

**Ejemplos del tipo de instrucciones que suelen usarse en este refactor:**

- Centralizar todas las llamadas HTTP del frontend en un módulo único; mantener OAuth2 con cuerpo form y errores compatibles con el campo `detail` de FastAPI.
- Actualizar `auth.ts` y `services.ts` para usar solo `apiJson` y `apiRequestForm`.
- Alinear la ruta de contacto con el router `prefix="/services"` en el backend.

### Código refactorizado (extractos)

Documentación del objetivo del módulo (`apiClient.ts`):

```typescript
/**
 * Capa única de llamadas HTTP al backend (FastAPI).
 *
 * Refactor (2025): Antes cada módulo (`auth.ts`, `services.ts`) repetía `fetch`,
 * cabeceras y manejo de errores. Eso facilitaba rutas mal escritas (p. ej.
 * `/service/contact`) y respuestas de error inconsistentes.
 */
```

Petición JSON con Bearer opcional y método por defecto:

```typescript
export async function apiJson<T>(path: string, options: JsonRequestOptions = {}): Promise<T> {
  const base = normalizeBaseUrl();
  const pathname = normalizePath(path);
  const { json, token } = options;
  let method = options.method ?? (json !== undefined ? "POST" : "GET");
  const headers: Record<string, string> = { "Cache-Control": "no-cache" };
  if (token) headers.Authorization = `Bearer ${token}`;
  // ... Content-Type y body JSON si aplica ...
  const res = await fetch(`${base}${pathname}`, { method, headers, body });
  return (await parseJsonOrThrow(res)) as T;
}
```

Autenticación OAuth2 vía form:

```typescript
async loginWithOauth(username: string, password: string): Promise<ITokenResponse> {
  const params = new URLSearchParams();
  params.append("username", username);
  params.append("password", password);
  return apiRequestForm<ITokenResponse>(`/login/oauth`, params);
}
```

Servicio de contacto alineado con FastAPI (`api.py` incluye `prefix="/services"` y el frontend usa `/services/contact`).

### Explicación breve de las mejoras

| Mejora | Efecto |
|--------|--------|
| Cliente HTTP único | Mantenimiento y consistencia. |
| `parseJsonOrThrow` + `detail` | Errores homogéneos para la UI. |
| `apiRequestForm` | Contrato correcto con OAuth2 password del backend. |
| Normalización de URL/path | Menos fallos por barras o base mal configurada. |
| Router `services` en backend | Mismo contrato que `apiService.postEmailContact`. |

### Material visual opcional para informes o presentaciones

- Captura del encabezado de `frontend/app/lib/api/apiClient.ts` donde se documenta el refactor.
- Captura de Swagger (`/docs`) para `POST /api/v1/login/oauth` y `POST /api/v1/services/contact`.
- Diagrama simple: navegador → `apiClient` → FastAPI → MongoDB.

### Conclusiones del análisis

La capa HTTP dispersa es una fuente habitual de deuda técnica en frontends que crecen. Centralizar en `apiJson` / `apiRequestForm` y unificar errores con la forma que devuelve FastAPI **reduce bugs de integración** y **acopla menos** los módulos de negocio al detalle de `fetch`. Las notas de versión de la plantilla (**ODMantic**, correcciones TOTP, auth en rutas) refuerzan la misma línea: contratos explícitos entre cliente y servidor. **Este informe y el refactor asociado fueron elaborados con apoyo de inteligencia artificial en Cursor**, conservando revisión humana del diseño y del código.

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

