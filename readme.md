## Descripción General

Esta actividad tiene como objetivo construir una **API con FastAPI** que demuestre el uso de:
- **Rutas básicas**
- **Parámetros de ruta**
- **Parámetros de consulta (queries)**

El proyecto se prueba desde el **navegador** y desde la **documentación automática** generada por FastAPI en `/docs`.

---

## Objetivo

Desarrollar una API que cumpla con las siguientes metas:

- Implementar rutas GET con **parámetros de tipo simple** (`str`, `int`, `float`, `bool`).
- Manejar correctamente los **valores por defecto** y las **validaciones de tipo**.
- Ejecutar la aplicación localmente y verificar las respuestas esperadas.
- Mostrar los endpoints en la documentación automática (`Swagger UI`).

---

## Tecnologías utilizadas

- **Python 3.10+**
- **FastAPI**
- **Uvicorn**
- **CORS Middleware** (opcional, si se conecta con frontend)

---

## Estructura del proyecto

sena-fastapi-actividad3/
│
├── main.py
└── README.md

---

## Contenido de `main.py`

El archivo `main.py` contiene la implementación principal de la API, cumpliendo las **10 rutas de prueba** definidas en el enunciado.

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from typing import Optional

# ==============================================
# Instancia principal de la aplicación
# ==============================================
app = FastAPI(title="SENA FastAPI Actividad 3")

# ==============================================
# Configuración de CORS (opcional)
# ==============================================
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# ==============================================
# RUTA 1: Raíz del proyecto
# GET /
# ==============================================
@app.get("/")
def read_root():
    return {"mensaje": "Bienvenido a la API de FastAPI - Actividad 3"}

# ==============================================
# RUTAS 2, 3, 6, 9: /item/{item_id}
# Parámetros de ruta y de consulta
# ==============================================
@app.get("/item/{item_id}")
def read_item(
    item_id: int,
    q: Optional[str] = None,
    activo: bool = True
):
    return {"item_id": item_id, "q": q, "activo": activo}

# ==============================================
# RUTAS 4 y 7: /saludo/{nombre}
# Parámetro de ruta tipo string
# ==============================================
@app.get("/saludo/{nombre}")
def saludo(nombre: str):
    return {"saludo": f"¡Hola, {nombre}!"}

# ==============================================
# RUTAS 5, 8 y 10: /config
# Parámetros de consulta (modo, version)
# ==============================================
@app.get("/config")
def config(
    modo: str = "produccion",
    version: float = 1.0
):
    return {"modo": modo, "version": version}
```

---

## Ejecución del proyecto

### 1️⃣ Instalar dependencias

Asegúrate de tener instalado Python y pip. Luego ejecuta:

```bash
pip install fastapi uvicorn
```

### 2️⃣ Ejecutar el servidor

Dentro del directorio del proyecto:

```bash
uvicorn main:app --reload
```

### 3️⃣ Probar la API

* Página raíz: [http://localhost:8000](http://localhost:8000)
* Documentación automática: [http://localhost:8000/docs](http://localhost:8000/docs)

---

## Endpoints implementados

| Método | Ruta               | Descripción                                                                                            |
| ------ | ------------------ | ------------------------------------------------------------------------------------------------------ |
| GET    | `/`                | Devuelve mensaje de bienvenida                                                                         |
| GET    | `/item/{item_id}`  | Retorna un item por su ID con parámetros de consulta opcionales (`q`, `activo`)                        |
| GET    | `/saludo/{nombre}` | Devuelve un saludo personalizado                                                                       |
| GET    | `/config`          | Devuelve los valores de configuración (`modo`, `version`) con valores por defecto o definidos en query |

---

## Pruebas esperadas

| URL                                | Resultado esperado                                      |
| ---------------------------------- | ------------------------------------------------------- |
| `/`                                | `{"mensaje": "Bienvenido..."}`                          |
| `/item/42`                         | `{"item_id": 42, "q": null, "activo": true}`            |
| `/item/10?q=busqueda&activo=false` | `{"item_id": 10, "q": "busqueda", "activo": false}`     |
| `/saludo/María`                    | `{"saludo": "¡Hola, María!"}`                           |
| `/config?modo=dev&version=2.5`     | `{"modo": "dev", "version": 2.5}`                       |
| `/item/0?q=&activo=true`           | `{"item_id": 0, "q": "", "activo": true}`               |
| `/saludo/José%20Luis`              | `{"saludo": "¡Hola, José Luis!"}`                       |
| `/config`                          | `{"modo": "produccion", "version": 1.0}`                |
| `/item/-7`                         | `{"item_id": -7, "q": null, "activo": true}`            |
| `/config?modo=test&version=abc`    | `422 Unprocessable Entity (error de tipo en "version")` |

---

## Verificación en `/docs`

1. Ejecuta la aplicación (`uvicorn main:app --reload`).
2. Abre [http://localhost:8000/docs](http://localhost:8000/docs).
3. Observa los 4 endpoints definidos.
4. Prueba las diferentes combinaciones de parámetros y valida los resultados.

---

## Explicación técnica

* **Rutas** (`/`, `/item/{item_id}`, `/saludo/{nombre}`, `/config`)
  Demuestran el manejo de path parameters (`{item_id}`, `{nombre}`).

* **Parámetros de consulta** (`q`, `activo`, `modo`, `version`)
  Permiten probar cómo FastAPI interpreta automáticamente tipos (`int`, `bool`, `float`, `str`).

* **Validación automática de tipos**
  FastAPI devuelve un error `422 Unprocessable Entity` cuando un parámetro tiene tipo incorrecto (por ejemplo, `version=abc` en `/config`).

---
