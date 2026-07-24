# Estructura del proyecto

Este documento describe la estructura del repositorio "Taller-DevOps-Monorepo-CI" y explica brevemente el propósito de cada carpeta/archivo principal. El repositorio es un monorepo con un backend en Python (FastAPI) y un frontend en React + TypeScript, además de configuraciones para Docker y CI.

---
##haciendo cambio eliminando comentario  
##otro intento 
##archivo de arbol resumido 

```
Taller-DevOps-Monorepo-CI/
├── .github/
│   └── workflows/
│       └── ci.yml
├── .gitignore
├── README.md
├── docker-compose.yaml
├── backend/
│   ├── Dockerfile
│   ├── README.md
│   ├── requirements.txt
│   ├── main.py
│   ├── app/
│   │   ├── __init__.py
│   │   ├── core/
│   │   │   ├── __init__.py
│   │   │   ├── config.py
│   │   │   ├── logger.py
│   │   │   ├── middleware.py
│   │   │   └── security.py
│   │   ├── routers/
│   │   │   ├── __init__.py
│   │   │   ├── auth_router.py
│   │   │   ├── calculadora_router.py
│   │   │   └── health_router.py
│   │   ├── schemas/
│   │   │   ├── __init__.py
│   │   │   ├── auth.py
│   │   │   └── calculadora.py
│   │   └── services/
│   │       ├── __init__.py
│   │       └── calculadora_service.py
│   └── tests/
│       ├── __init__.py
│       ├── conftest.py
│       ├── test_auth.py
│       ├── test_calculadora.py
│       ├── test_cors.py
│       └── test_health.py
└── frontend/
    ├── Dockerfile
    ├── README.md
    ├── nginx.conf.template
    ├── package.json
    ├── tsconfig.json
    ├── public/
    │   └── index.html
    └── src/
        ├── App.tsx
        ├── App.css
        ├── Calculador.tsx
        ├── Calculadora.css
        ├── index.tsx
        ├── index.css
        ├── react-app-env.d.ts
        ├── reportWebVitals.ts
        ├── setupTests.ts
        ├── global.d.ts
        ├── services/
        │   └── api.ts
        ├── components/
        │   ├── Display.tsx
        │   ├── Historial.tsx
        │   ├── NumberPad.tsx
        │   ├── OperationPad.tsx
        │   └── StatusBar.tsx
        ├── hooks/
        │   ├── index.ts
        │   ├── useAuth.ts
        │   └── useCalculadora.ts
        ├── types/
        │   └── index.ts
        └── assets/
            └── image/
                └── mate.png
```

---

## Explicación por carpetas y archivos principales

### Raíz
- `docker-compose.yaml` — Orquesta los servicios (backend y frontend) para desarrollo/CI. (El flujo de CI usa `docker compose build` y `docker compose run --rm backend pytest`.)
- `.github/workflows/ci.yml` — Configuración de GitHub Actions (se ejecuta en PR a `main`; en este repo construye los contenedores y ejecuta tests del backend).
- `README.md` — Documentación general del proyecto.

### Backend (`backend/`)
Descripción: API REST escrita con FastAPI.

- `backend/main.py` — Punto de entrada de la aplicación FastAPI. Configura:
  - `root_path` en `/api` (por eso las rutas y docs se sirven bajo `/api`).
  - `docs_url` y `redoc_url` (`/docs` y `/redoc` sobre la raíz definida, p. ej. `/api/docs`).
  - `CORSMiddleware` para permitir peticiones desde el frontend (orígenes configurados en `app/core/config.py`).
  - Un `LoggingMiddleware` y el registro del ciclo de vida (lifespan).

- `backend/requirements.txt` — Dependencias: `fastapi`, `uvicorn`, `pydantic` (v2), `pydantic-settings`, `python-jose` (para auth), `python-multipart`. Para testing: `pytest`, `httpx`.

- `backend/app/core/` — Código compartido y configuración:
  - `config.py` — Variables de configuración (ej. `CORS_ORIGINS`).
  - `logger.py` — Configuración del logger.
  - `middleware.py` — Middlewares personalizados (ej. logging).
  - `security.py` — Utilidades de seguridad/autenticación.

- `backend/app/routers/` — Routers de FastAPI (endpoints):
  - `health_router.py` — Endpoints de health check.
  - `auth_router.py` — Endpoints de autenticación.
  - `calculadora_router.py` — Endpoints principales de la calculadora.

- `backend/app/schemas/` — Modelos Pydantic para request/response (ej. `auth.py`, `calculadora.py`).

- `backend/app/services/` — Lógica de negocio separada (ej. `calculadora_service.py`).

- `backend/tests/` — Tests automatizados con `pytest` y `httpx`.

### Frontend (`frontend/`)
Descripción: Aplicación React + TypeScript (creada con Create React App / `react-scripts`).

- `frontend/package.json` — Dependencias y scripts (`start`, `build`, `test`). Usa React 19 y TypeScript.
- `frontend/src/` — Código fuente:
  - `components/` — Componentes UI (Display, NumberPad, StatusBar, etc.).
  - `hooks/` — Hooks personalizados (`useAuth`, `useCalculadora`).
  - `services/api.ts` — Cliente centralizado para llamadas a la API.
  - `assets/` — Imágenes y recursos estáticos.
  - `setupTests.ts` y archivos `*.test.tsx` — Tests con Testing Library.
- `nginx.conf.template` — Plantilla para la configuración de Nginx en producción.
- `Dockerfile` — Imagen del frontend para producción.

---

## Cómo ejecutar (resumen rápido)

Nota: revisa `README.md` para instrucciones específicas. A continuación se muestran formas comunes:

1) Con Docker Compose (recomendado para reproducibilidad):

   ```bash
   # Desde la raíz del repo
   docker compose build
   docker compose up -d
   # Para ejecutar tests del backend (igual que en CI)
   docker compose run --rm backend pytest
   ```

2) Ejecutar local sin Docker

   Backend (local):
   ```bash
   cd backend
   python -m venv .venv
   source .venv/bin/activate   # o .venv\Scripts\activate en Windows
   pip install -r requirements.txt
   # Ejecutar con uvicorn desde dentro de la carpeta backend
   uvicorn main:app --reload --host 0.0.0.0 --port 8000
   # La API está bajo la ruta base /api → docs: http://localhost:8000/api/docs
   ```

   Frontend (local):
   ```bash
   cd frontend
   npm install
   npm start
   # Por defecto CRA sirve en http://localhost:3000 (si no hay colisiones)
   ```

---
