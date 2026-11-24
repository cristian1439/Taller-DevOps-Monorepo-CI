# Estructura del proyecto

Este documento describe la estructura del repositorio "Taller-DevOps-Monorepo-CI". Es un
monorepo con un backend en Python (FastAPI) y un frontend en React + TypeScript, además de
configuraciones para Docker y CI.

---

## Árbol de archivos (resumido)
Taller-DevOps-Monorepo-CI/
├── .github/workflows/ci.yml
├── .gitignore
├── README.md
├── docker-compose.yaml
├── backend/
│ ├── Dockerfile
│ ├── requirements.txt
│ ├── main.py
│ ├── app/
│ │ ├── core/ (config, logger, middleware, security)
│ │ ├── routers/ (auth, calculadora, health)
│ │ ├── schemas/ (auth, calculadora)
│ │ └── services/ (calculadora_service)
│ └── tests/ (conftest, test_auth, test_calculadora, test_cors, test_health)
└── frontend/
├── Dockerfile
├── nginx.conf.template
├── package.json
└── src/
├── components/ (Display, Historial, NumberPad, OperationPad, StatusBar)
├── hooks/ (useAuth, useCalculadora)
├── services/ (api.ts)
└── types/


---

## Explicación

- `docker-compose.yaml`: orquesta los servicios backend y frontend. El CI usa
  `docker compose build` y `docker compose run --rm backend pytest`.
- `.github/workflows/ci.yml`: workflow de GitHub Actions que se ejecuta en cada pull request
  hacia `main`; construye los contenedores y ejecuta las pruebas del backend.
- `backend/`: API REST con FastAPI, autenticación JWT y validación con Pydantic.
- `backend/tests/`: pruebas automatizadas con pytest.
- `frontend/`: aplicación React + TypeScript servida con nginx como proxy reverso.

- `main` está protegida: requiere PR, aprobación y que pase el CI.
