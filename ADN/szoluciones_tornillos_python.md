# SZoluciones — Tornillos Python
### Decisiones y patrones del stack Python (FastAPI, SQLAlchemy, multi-tenant, 72 tornillos)

> Este archivo documenta los 72 tornillos del stack Python de SZoluciones, organizados en 10 áreas. Los tornillos equivalentes a universales están indicados. Los principios completos de los universales equivalentes viven en `szoluciones_tornillos_universales.md`.

> **Mapeo por categoría de decisión:** Ver la tabla en `CLAUDE.md` (FASE 3, PASO 2) para saber qué tornillos revisar según la naturaleza del proyecto.

---

## Índice por área

### ÁREA 1 — EL PLANO DE LA CASA (estructura, routing, organización)
| # | Título |
|---|--------|
| 1 | El composition root — Centralizar el registro de routers en un solo lugar, sin auto-discovery |
| 2 | Capas estrictas — Routes finas, lógica de negocio en services |
| 3 | Fachada `__init__` — Re-exportar la API pública via `__all__`, ocultar internos con `_` |
| 4 | Archivos auto-generados — Agregar header banner documentando origen y método de regeneración |
| 72 | Proveedores intercambiables — Usar ABC + Strategy pattern, factory selecciona por env var |

### ÁREA 2 — LA PUERTA Y EL PORTERO (autenticación y autorización)
| # | Título |
|---|--------|
| 5 | 404 en vez de 403 — Recursos multi-tenant de otro tenant devuelven 404, no revelan existencia |
| 6 | Scoping por tenant — Toda query filtra por tenant_id del token del usuario autenticado |
| 7 | Fail-closed en `get_usuario_actual` — Revalidar existencia/estado activo del usuario contra la DB |
| 8 | Validar config crítica al boot — Rechazar secretos débiles y algoritmos inválidos al importar |
| 9 | Detección de secretos débiles — Verificar longitud mínima y strings de placeholder |
| 10 | Dependency factory para roles — Factory function que retorna dependency de chequeo de rol |
| 11 | Scope helper como guard previo — Validar ownership antes de mutar recursos |
| 12 | bcrypt con fallback PBKDF2 — Detectar schema por prefijo del hash, usar comparación tiempo-constante |
| 13 | 401 global en el cliente — Interceptor único maneja expiración de token, limpia sesión |
| 71 | Ruteo de tenant fail-closed — Resolver tenant de webhook desde integración activa, rechazar si falta |

### ÁREA 3 — EL VIAJE DE UN DATO (validación, transacciones, serialización)
| # | Título |
|---|--------|
| 14 | Schema de entrada distinto del de salida — Separar Create/Update del schema Response |
| 15 | El commit vive en el handler — Services hacen flush(), handlers hacen commit() y refresh() |
| 16 | Validación en capas — Pydantic para estructura, services para reglas que dependen de DB |
| 17 | `ValueError` del service → `HTTPException` — Services tiran ValueError, handlers traducen a HTTP |
| 18 | Lock pesimista — Usar `with_for_update()` para transiciones de estado con efectos financieros |
| 19 | La salida nunca es el ORM crudo — Declarar response_model, usar json_encoder para datetime |
| 20 | El cliente normaliza — Frontend convierte fechas a ISO-UTC, parsea arrays de errores de Pydantic |

### ÁREA 4 — EL ARCHIVO DEL NEGOCIO (modelos, DB, dinero)
| # | Título |
|---|--------|
| 21 | PK uuid como String — Toda PK es String con UUID, nunca autoincrement |
| 22 | Dinero con Numeric/Decimal — Usar Numeric(12,2) para montos, nunca Float |
| 23 | Timestamps — created_at default, updated_at default+onupdate con callable compartido |
| 24 | Enums — Modelar con `class X(str, Enum)` y `SQLEnum(X)` en la columna |
| 25 | Índices y unicidad tenant-first — Índices/constraints compuestos empiezan por el tenant_id |
| 26 | Encriptación transparente — Guardar en columna `_encrypted`, exponer via hybrid_property |
| 27 | Pool de conexiones endurecido — Configurar pre_ping, recycle, LIFO, size via env |
| 28 | `init_db` detecta drift — Fallar si faltan columnas, solo auto-crear tablas bajo flag |

### ÁREA 5 — CUANDO ALGO SE ROMPE (errores, logging, resiliencia)
| # | Título |
|---|--------|
| 29 | Stacktrace adentro, mensaje genérico afuera — Loguear excepción, retornar detail genérico seguro |
| 30 | Un logger por módulo — Declarar `getLogger(__name__)`, elegir nivel por severidad |
| 31 | Fail-closed en firmas/tokens — Validar con comparación tiempo-constante, rechazar con 403 |
| 32 | Proveedor externo caído — Capturar excepciones, retornar fallback degradado, loguear error |
| 33 | Cron con try/rollback/finally — Jobs manejan sesión, cuentan errores, nunca re-lanzan |
| 34 | Error boundary + 401 interceptor — Frontend captura errores de render, centraliza expiración |

### ÁREA 6 — LA LETRA DEL SENIOR (estilo, naming, helpers)
| # | Título |
|---|--------|
| 35 | Docstring de módulo en español — Iniciar cada archivo con triple-quoted descripción del módulo |
| 36 | Helpers `_env_bool`/`_env_int` — Parsear y validar env vars con defaults |
| 37 | `typing.cast` — Envolver acceso a atributos ORM para mantener precisión de tipos |
| 38 | Triada `get_`/`require_`/`upsert_` — Naming canónico de funciones por verbo por entidad |
| 39 | Guard clauses — Validar y tirar HTTPException temprano, sin else anidado |
| 40 | Helpers privados `_` — Extraer micro-operaciones reutilizables (format, parse, normalize) |
| 41 | Type hints completos — Anotar todos los parámetros y returns; keyword-only para args de dominio |
| 42 | Raw SQL confinado — Sin SQL ad-hoc; solo parametrizado text() en migraciones |
| 43 | Imports explícitos de helpers — Importar `_helpers` por nombre, no via wildcard |

### ÁREA 7 — LAS LLAVES DEL LOCAL (config, secrets, ambiente)
| # | Título |
|---|--------|
| 44 | `.env.example` versionado — Commitear template con todas las vars; ignorar el `.env` real |
| 45 | Comandos para generar secretos — Incluir comando de generación como comentario en el template |
| 46 | Defaults distintos por ambiente — Derivar defaults de la variable ENV |
| 47 | Validación fail-fast de config — Validar config crítica al importar, lanzar RuntimeError |
| 48 | Secretos productivos inyectados — Usar generateValue/sync:false/fromDatabase en IaC |
| 49 | Normalizar `DATABASE_URL` — Convertir postgres:// a postgresql://, validar driver |
| 50 | Defaults de runtime en start.sh — Script central con bash defaults, correr migraciones una vez |

### ÁREA 8 — CUANDO ESTÁ TERMINADO (tests, CI, cobertura)
| # | Título |
|---|--------|
| 51 | El nombre del test ES la documentación — Nombrar tests por la regla de negocio, no por la función |
| 52 | Fixture `db_session` efímera — SQLite en memoria creada/destruida por test |
| 53 | `monkeypatch` para cortar IO — Reemplazar llamadas externas con spy-fakes que rastrean argumentos |
| 54 | Fixture global autouse — Fixture única en conftest mata caché no-determinístico |
| 55 | Idempotencia y reintentos como invariante — Testear que llamar la operación dos veces no duplica |
| 56 | `tests/manual` excluido — Scripts que dependen del entorno operacional en carpeta separada, fuera de CI |
| 57 | CI gatea por cobertura — Matrix test contra DB real, enforce cobertura mínima |
| 58 | Reloj y entorno deterministas — monkeypatch del clock, auto-generar secretos en archivos de test |

### ÁREA 9 — LO QUE PYTHON HACE MEJOR (patrones pythónicos)
| # | Título |
|---|--------|
| 59 | Generator como recurso — Usar generator con yield y finally para cleanup de sesión DB |
| 60 | Imports opcionales con degradación — try/except ModuleNotFoundError, proveer fallback |
| 61 | Context managers de SQLAlchemy — Usar with `engine.begin()`/`connect()`, ajustar isolation_level |
| 62 | Registry inmutable con dataclasses frozen — Tupla de definiciones de acción agrupadas con matcher/prepare/execute |
| 63 | `@asynccontextmanager` para lifespan — Generator async único maneja startup/shutdown de la app |

### ÁREA 10 — CÓMO CRECE ESTO (migraciones, integraciones, escalado)
| # | Título |
|---|--------|
| 64 | El orden sagrado de 8 capas — Model → migration → schemas → service → route → main.py → client → test |
| 65 | Migración idempotente con inspector — Verificar existencia antes de CREATE/DROP, re-fetch inspector post-DDL |
| 66 | Enum Postgres idempotente — Usar DO $$ ... EXCEPTION WHEN duplicate_object block |
| 67 | Migración encadenada — Nombrar YYYYMMDD_NN, formar cadena lineal, agregar migraciones de rollback |
| 68 | Guardrails por nivel de riesgo — Declarar risk_level, requerir confirmación para operaciones sensibles |
| 69 | Integración paralela clonable — Replicar template de carpeta de integración, usar facade |
| 70 | Policy chain con precedencia — Objetos de policy ordenados con snapshot y resolución de conflictos |

---

## Tornillos clave con principio completo

### TORNILLO #5 — 404 en vez de 403 para recursos de otro tenant

**En una línea:** Los recursos que existen pero pertenecen a otro tenant devuelven 404, no 403, para no revelar si el recurso existe.

**La regla para el próximo proyecto:** En cualquier endpoint que busca un recurso por ID, filtrá siempre por `AND tenant_id = <del token>`. Si la query devuelve None (porque el ID no existe O porque es de otro tenant), respondé `raise HTTPException(status_code=404)`. Nunca respondas 403 con un mensaje como "no tenés acceso a este recurso" porque eso confirma que el recurso existe.

**Señal de que lo estás usando bien:** Un request de tenant A con un ID válido de tenant B recibe 404, no 403 ni los datos.

---

### TORNILLO #7 — Fail-closed en `get_usuario_actual`

**En una línea:** La dependency que extrae el usuario del token no se queda solo con lo que viene en el JWT: re-consulta la DB para verificar que el usuario sigue existiendo y activo.

**La regla para el próximo proyecto:** La FastAPI dependency `get_usuario_actual` hace: (1) decodificar y validar el JWT, (2) hacer un SELECT a la DB buscando el usuario por id, (3) si no existe o `is_active=False`, lanzar 401. No confíes en que el token tiene información vigente: un usuario borrado o desactivado sigue teniendo un token válido hasta que expire.

---

### TORNILLO #14 — Schema de entrada distinto del de salida

**En una línea:** Los schemas de Pydantic para crear/actualizar (`XCreate`, `XUpdate`) son distintos al schema de respuesta (`XResponse`), que tiene `from_attributes=True` y solo expone los campos que el cliente debe ver.

**La regla para el próximo proyecto:** Definí tres schemas por entidad: `XCreate` (campos requeridos para crear), `XUpdate` (todos opcionales con `Optional[T] = None`), y `XResponse` (lo que devuelve el endpoint, con `model_config = ConfigDict(from_attributes=True)`). Los endpoints declaran `response_model=XResponse` para garantizar que el ORM no se filtra crudo.

---

### TORNILLO #15 — El commit vive en el handler

**En una línea:** Los services solo hacen `db.flush()` (para obtener IDs generados); el `db.commit()` y el `db.refresh()` son responsabilidad del handler de la route.

**La regla para el próximo proyecto:** Los services reciben la sesión de DB y llaman `db.flush()` al final. El handler (route) llama `db.commit()` después de que TODOS los services terminaron, y luego `db.refresh(objeto)` si necesita los valores generados por la DB. Esto permite que un handler que llama a dos services agrupe todo en una sola transacción.

---

### TORNILLO #18 — Lock pesimista para transiciones de estado con efectos financieros

**En una línea:** Antes de modificar una entidad que dispara efectos financieros (débitos, acreditaciones, cambios de estado de pago), lockeá la fila con `SELECT ... FOR UPDATE` para evitar condiciones de carrera.

**La regla para el próximo proyecto:** En cualquier operación que (1) lee el estado actual de una entidad y (2) escribe un nuevo estado o mueve plata basándose en ese estado, usá `db.execute(select(X).where(...).with_for_update())`. Esto garantiza que dos requests concurrentes no lean el mismo estado "pre-cambio" y ambos procedan.

---

### TORNILLO #32 — Proveedor externo caído → fallback degradado

**En una línea:** Cuando un proveedor externo (LLM, API de tercero, WhatsApp) no responde, el sistema captura la excepción, loguea el error, y retorna un resultado degradado en vez de propagar el error al usuario.

**La regla para el próximo proyecto:** Para toda integración con proveedor externo: envolvé el call en try/except, loguéalo con nivel ERROR, y retorná un valor de fallback definido (None, lista vacía, respuesta por defecto). El endpoint no explota: responde con lo que tiene y el usuario sabe que hubo un problema.

---

### TORNILLO #62 — Registry inmutable con dataclasses frozen (para agentes con acciones)

**En una línea:** Las acciones que un agente puede ejecutar se definen como dataclasses frozen en una tupla inmutable, donde cada acción tiene matcher, prepare y execute.

**La regla para el próximo proyecto:** Si construís un agente que ejecuta acciones sobre el sistema, definí cada acción como `@dataclass(frozen=True)` con métodos `matcher(input) -> bool`, `prepare(input) -> Params`, `execute(params, db) -> Result`. Registralas en una tupla global inmutable. El motor del agente itera el registry y ejecuta la primera que matchea.

---

### TORNILLO #68 — Guardrails por nivel de riesgo

**En una línea:** Las acciones que puede ejecutar un agente tienen un `risk_level` declarado; las de nivel alto requieren confirmación explícita del usuario antes de ejecutarse.

**La regla para el próximo proyecto:** Declará `risk_level: Literal['low', 'medium', 'high']` en cada acción del registry. El motor del agente, antes de ejecutar una acción con `risk_level='high'`, retorna al usuario un resumen de lo que va a hacer y espera confirmación. Las acciones de bajo riesgo se ejecutan directamente.

---

### TORNILLO #72 — Proveedores intercambiables con ABC + Strategy

**En una línea:** Cuando un sistema puede usar múltiples proveedores para el mismo servicio (email, pago, LLM, SMS), cada uno implementa una ABC; un factory selecciona el proveedor por env var.

**La regla para el próximo proyecto:** Definí una ABC con los métodos del servicio. Implementá un proveedor concreto por cada provider. Creá un factory `get_provider() -> ABCBase` que lee la env var `X_PROVIDER=twilio|sendgrid|...` y retorna la instancia correcta. El resto del código depende solo de la ABC.

---

## Notas para el mapeo

**Cuando hay usuarios + auth:** Los tornillos clave son #5 (404 vs 403), #6 (scope por tenant), #7 (fail-closed en get_usuario_actual), #8 y #9 (validación de config y secretos al boot).

**Cuando hay base de datos:** Los tornillos clave son #21 (UUID como PK), #22 (Decimal para dinero), #23 (timestamps), #25 (índices tenant-first), #27 (pool endurecido), #28 (init_db con drift detection).

**Cuando hay integración con proveedor externo:** #32 (proveedor caído → fallback), #72 (ABC + Strategy), #31 (firma/token fail-closed), #18 (lock pesimista para efectos financieros).

**Cuando hay un agente que ejecuta acciones:** #62 (registry), #68 (guardrails por riesgo), #72 (proveedores intercambiables), #18 (lock para efectos financieros).

**Cuando hay tests:** #51 (nombre = regla de negocio), #52 (fixture SQLite efímera), #53 (monkeypatch para IO), #55 (idempotencia como invariante), #57 (CI con cobertura).
