# Problemas SonarCloud — val4nti_atlas (rama `main`)

Fuente: https://sonarcloud.io/summary/overall?id=val4nti_atlas&branch=main
Extraídos vía API (`/api/issues/search?componentKeys=val4nti_atlas&branch=main&resolved=false`) el 2026-10-10.

**Total: 31 problemas abiertos — 31 corregidos.**

| Tipo | Cantidad |
|---|---|
| Bug | 10 |
| Vulnerabilidad | 12 |
| Code smell | 9 |

## Listado y solución

| # | Severidad | Tipo | Regla | Archivo:línea | Problema | Solución aplicada |
|---|---|---|---|---|---|---|
| 1 | MAJOR | Bug | css:S4649 | `FRONTEND/src/index.css:6` | Falta familia genérica de fuente (`"sans-serif"` estaba entre comillas, se trataba como nombre de fuente) | Se cambió a `sans-serif` sin comillas |
| 2 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/Home.jsx:77` | Promesa no esperada | `void fetchMetricas();` |
| 3 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/clientes/ClienteForm.jsx:63` | Promesa no esperada (IIFE async) | `void (async () => {...})()` |
| 4 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/contratos/ContratoDetalle.jsx:21` | Promesa no esperada | `void load();` |
| 5 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/contratos/ContratoForm.jsx:34` | Promesa no esperada (IIFE async) | `void (async () => {...})()` |
| 6 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/contratos/ContratoForm.jsx:46` | Promesa no esperada (IIFE async) | `void (async () => {...})()` |
| 7 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/contratos/ContratosTabCreate.jsx:27` | Promesa no esperada (IIFE async) | `void (async () => {...})()` |
| 8 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/empresas/EmpresaForm.jsx:33` | Promesa no esperada (IIFE async) | `void (async () => {...})()` |
| 9 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/empresas/EmpresasList.jsx:50` | Promesa no esperada | `void load()` |
| 10 | MAJOR | Bug | javascript:S9383 | `FRONTEND/src/pages/usuarios/UsuarioForm.jsx:36` | Promesa no esperada (IIFE async) | `void (async () => {...})()` |
| 11 | MAJOR | Vulnerabilidad | docker:S6505 | `BACKEND/Dockerfile:23` | `npm install -g` sin `--ignore-scripts` | Se eliminó la línea basura (`npm install -g @npm/example`, además de los `npx some-package` de prueba); se instala con `npm ci --omit=dev --ignore-scripts` |
| 12 | MINOR | Vulnerabilidad | docker:S6471 | `BACKEND/Dockerfile:1` | Imagen `node` corre como root | Se agregó `USER node` (y `chown` de `uploads/`) |
| 13 | MINOR | Vulnerabilidad | javascript:S5689 | `BACKEND/src/app.js:20` | Express expone versión (`X-Powered-By`) | `app.disable("x-powered-by")` |
| 14 | MAJOR | Vulnerabilidad | javascript:S5122 | `BACKEND/src/app.js:22` | CORS abierto a cualquier origen | `cors({ origin: allowedOrigins })` leyendo `CORS_ORIGINS` (por defecto `http://localhost:3350,http://localhost:5173`); se añadió `CORS_ORIGINS` en `docker-compose.yml` |
| 15 | MAJOR | Vulnerabilidad | javascript:S5693 | `BACKEND/src/routes/contracts.js:15` | Subida de archivos (multer) sin límite de tamaño | `limits: { fileSize: 5 MB }` (Sonar marca como inseguro cualquier límite mayor a 8 MB) |
| 16 | MAJOR | Vulnerabilidad | docker:S8543 | `FRONTEND/Dockerfile:15` | Dependencias sin versiones bloqueadas (se borraba `package-lock.json`) | Se usa `npm ci` con el lockfile |
| 17 | MAJOR | Vulnerabilidad | docker:S6505 | `FRONTEND/Dockerfile:15` | Instalación sin `--ignore-scripts` | `npm ci --ignore-scripts` |
| 18 | MAJOR | Vulnerabilidad | docker:S8543 | `FRONTEND/Dockerfile:16` | `npm install @rollup/...` sin lockfile | Eliminado (el lockfile ya incluye los binarios opcionales de rollup/esbuild) |
| 19 | MAJOR | Vulnerabilidad | docker:S6505 | `FRONTEND/Dockerfile:16` | Instalación sin `--ignore-scripts` | Eliminado |
| 20 | MAJOR | Vulnerabilidad | docker:S8543 | `FRONTEND/Dockerfile:28` | `npm install esbuild` sin lockfile | Eliminado el paso de reinstalación de esbuild |
| 21 | MAJOR | Vulnerabilidad | docker:S6505 | `FRONTEND/Dockerfile:28` | Instalación sin `--ignore-scripts` | Eliminado |
| 22 | MINOR | Vulnerabilidad | docker:S6471 | `FRONTEND/Dockerfile:34` | Imagen `nginx` corre como root | Se usa `nginxinc/nginx-unprivileged:alpine` + `USER nginx`, puerto 8080 (`nginx.conf` y `docker-compose.yml` actualizados) |
| 23–27 | MINOR | Code smell | javascript:S7773 | `BACKEND/src/routes/contracts.js:213,220,232,244,256` | `parseInt` global | `Number.parseInt` |
| 28–31 | MINOR | Code smell | javascript:S7773 | `BACKEND/src/routes/metricas.js:130,137,149,161` | `parseInt` global | `Number.parseInt` |

## Cambios adicionales

- `BACKEND/Dockerfile`: se quitó `COPY uploads/ ./uploads/`, porque la carpeta no existe en el repo y rompía el build (se crea con `mkdir -p`).
- `FRONTEND/.dockerignore`: nuevo archivo; evita copiar `node_modules`/`dist` locales a la imagen.

## Verificación

- `node --check` sobre `app.js`, `contracts.js` y `metricas.js`: OK.
- Frontend: `npm ci --ignore-scripts` + `npm run build`: build OK (confirma que esbuild/rollup funcionan sin scripts de instalación).
- Builds de Docker **no verificados**: Docker Desktop no estaba corriendo.
- El estado en SonarCloud se actualizará cuando se haga push y se ejecute un nuevo análisis.
