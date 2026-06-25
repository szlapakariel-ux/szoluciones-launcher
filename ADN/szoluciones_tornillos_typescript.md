# SZoluciones — Tornillos TypeScript
### Decisiones y patrones del stack TypeScript (monorepo Movete, 60 tornillos)

> Este archivo documenta los 60 tornillos del stack TypeScript de SZoluciones. Los tornillos marcados como universales también aparecen en `szoluciones_tornillos_universales.md` con su texto completo. Los que son Solo-TypeScript o Solo-este-proyecto solo viven acá.

> **Mapeo por categoría de decisión:** Ver la tabla en `CLAUDE.md` (FASE 3, PASO 2) para saber qué tornillos revisar según la naturaleza del proyecto.

---

## Índice completo (60 tornillos)

| # | Título | Categoría |
|---|--------|-----------|
| 1 | El package interno se referencia con workspace:* y se importa con alias @scope/* | ÁREA 1 — Monorepo |
| 2 | apps/ son deployables, packages/ son librerías, y se nota en el build | ÁREA 1 — Monorepo |
| 3 | tsconfig raíz único + override por proyecto | ÁREA 1 — Monorepo |
| 4 | Turbo orquesta respetando el grafo (^build) y cachea solo lo determinístico | ÁREA 1 — Monorepo |
| 5 | JWT flaco para identidad, DB gorda para permisos | ÁREA 2 — Auth |
| 6 | Cadena onRequest: primero autenticar, después autorizar (y de paso cobrar) | ÁREA 2 — Auth |
| 7 | Access token en memoria, refresh token persistido y rotado | ÁREA 2 — Auth |
| 8 | Catálogo de permisos como array de strings, fuente única compartida | ÁREA 2 — Auth |
| 9 | El org_id sale SIEMPRE del JWT, nunca del cliente | ÁREA 2b — Multi-tenant |
| 10 | Cada query carga su propio WHERE organization_id (filtro app-layer omnipresente) | ÁREA 2b — Multi-tenant |
| 11 | Fail-closed: sin contexto de tenant, cero datos | ÁREA 2b — Multi-tenant |
| 12 | RLS de Postgres con GUC como red de seguridad de DB (defensa en profundidad) | ÁREA 2b — Multi-tenant |
| 13 | La transacción como guardián de la regla de negocio | ÁREA 3 — Datos |
| 14 | organization_id en cada query, sin excepción | ÁREA 3 — Datos |
| 15 | Validar con Zod safeParse antes de tocar la DB | ÁREA 3 — Datos |
| 16 | El cliente tolera el envelope, no lo asume | ÁREA 3 — Datos |
| 17 | Invalidación dirigida de React Query con actualización optimista | ÁREA 3 — Datos |
| 18 | La plantilla de tabla multi-tenant (el molde que se copia y pega) | ÁREA 4 — DB |
| 19 | Índices deliberados sobre FK y deleted_at (no dejar que Postgres adivine) | ÁREA 4 — DB |
| 20 | onDelete con intención: Cascade para hijos, SetNull para histórico | ÁREA 4 — DB |
| 21 | RLS como última línea fail-closed (defense in depth en la base) | ÁREA 4 — DB |
| 22 | Conexiones como singleton con shutdown ordenado, health check y escape hatch a pg crudo | ÁREA 4 — DB |
| 23 | Errores de negocio se atajan, errores del sistema explotan | ÁREA 5 — Errores |
| 24 | Envelope de error { message } en español, uniforme | ÁREA 5 — Errores |
| 25 | Validar en la frontera con Zod safeParse y traducir SIEMPRE igual | ÁREA 5 — Errores |
| 26 | Webhook de pago a prueba de duplicados (idempotencia + firma + verdad del proveedor) | ÁREA 5 — Errores |
| 27 | Refresh silencioso de token en 401 con deduplicación | ÁREA 5 — Errores |
| 28 | Factory de rutas: setupXRoutes(app) | ÁREA 6 — Senior |
| 29 | Fail-closed multi-tenant: nunca una query sin org_id + soft delete | ÁREA 6 — Senior |
| 30 | Validación con Zod safeParse y 400 estructurado antes del SQL | ÁREA 6 — Senior |
| 31 | Auto-migración ensureXTable/ensureXColumn en el arranque | ÁREA 6 — Senior |
| 32 | Autorización declarativa: requirePermission en el guard, y el front filtra con hasPermission | ÁREA 6 — Senior |
| 33 | El .env.example como contrato narrado, no como lista muerta | ÁREA 7 — Config |
| 34 | Secrets multiempresa cifrados con AES-256-GCM, nunca en texto plano | ÁREA 7 — Config |
| 35 | Fallbacks de env en cascada: DB -> env -> default local | ÁREA 7 — Config |
| 36 | Config de cliente como build-arg, config de servidor como runtime env | ÁREA 7 — Config |
| 37 | Feature flags con default apagado para lo caro o riesgoso | ÁREA 7 — Config |
| 38 | Test de integración con DB real, sin mocks (inject + seed) | ÁREA 8 — Tests |
| 39 | Probar el caso negativo y el contrato, no solo el happy path | ÁREA 8 — Tests |
| 40 | RLS como red de seguridad final, testeada como atacante | ÁREA 8 — Tests |
| 41 | QA por smoke tests contra entorno real, gateando el release | ÁREA 8 — Tests |
| 42 | Un solo paquete, tres puertas (barrel @scope/shared) | ÁREA 9 — Shared |
| 43 | Zod como fuente única: el validador ES el tipo | ÁREA 9 — Shared |
| 44 | Permisos como datos: el array const que tipa todo | ÁREA 9 — Shared |
| 45 | El espejo Prisma↔TS: enums de DB = union types de shared | ÁREA 9 — Shared |
| 46 | Contrato de respuesta y de identidad uniforme (ApiResponse / JwtPayload / RequestContext) | ÁREA 9 — Shared |
| 47 | setupXRoutes: el módulo es una función auto-instalable | ÁREA 9 — Shared |
| 48 | Guards gemelos read/manage al tope del módulo | ÁREA 9 — Shared |
| 49 | Endpoint CRUD canónico: safeParse + tenant + soft-delete + mapX | ÁREA 9 — Shared |
| 50 | Schema Zod compartido: Create + Update.partial() como fuente de verdad | ÁREA 9 — Shared |
| 51 | Registrar una vez en index.ts; gatear en Sidebar y hook en mobile | ÁREA 9 — Shared |
| 52 | Credenciales cifradas en reposo con clave derivada y cadena de fallback | Integraciones |
| 53 | Webhook idempotente que no confía en el cliente y re-verifica server-to-server | Integraciones |
| 54 | Aislamiento multi-tenant: la organización es ciudadano de primera clase en el dinero | Integraciones |
| 55 | Billing de la plataforma como gate de permisos con 402 | Integraciones |
| 56 | Fallback por entorno con bandera de origen explícita | Integraciones |
| 57 | Paginación con clamp defensivo y envelope { data, total, page, totalPages } | ÁREA TRANSVERSAL |
| 58 | Constructor incremental de WHERE + params para filtros opcionales | ÁREA TRANSVERSAL |
| 59 | CSV listo para Excel: BOM, CRLF, escapeo RFC y filename con fecha | ÁREA TRANSVERSAL |
| 60 | Jobs en background: gate por header-secret, run-log en DB y dedupe idempotente por índice único parcial | ÁREA TRANSVERSAL |

---

## Tornillos con principio completo

Los tornillos universales (#2, #5, #8, #9, #10, #11, #13, #14, #18, #19, #20, #23, #24, #26, #28, #29, #32, #33, #34, #37, #38, #39, #40, #41, #46, #52, #53, #54, #55, #57, #59, #60) tienen su texto completo en `szoluciones_tornillos_universales.md`.

A continuación los tornillos Solo-TypeScript o Solo-este-proyecto:

---

### TORNILLO #1 — El package interno se referencia con workspace:* y se importa con alias @scope/*

**En una línea:** Los paquetes internos del monorepo se declaran como dependencia con `workspace:*` y se importan con un alias `@scope/nombre`, nunca con rutas relativas entre carpetas.

**La regla para el próximo proyecto:** En un monorepo con pnpm/yarn workspaces, declará los packages internos en package.json con `"@scope/shared": "workspace:*"` y configurá el alias en tsconfig.json (paths) y en el bundler. Nunca importes entre apps/packages con rutas relativas (../../packages/shared). El alias permite mover carpetas sin cambiar imports.

**Señal de que lo estás usando bien:** grep de `'../../` en archivos de src no devuelve imports entre packages distintos; todo import cross-package usa `@scope/`.

---

### TORNILLO #3 — tsconfig raíz único + override por proyecto

**En una línea:** Un tsconfig base en la raíz define strict y el resto de las reglas comunes; cada package/app lo extiende y solo sobreescribe lo que difiere.

**La regla para el próximo proyecto:** Definí un `tsconfig.base.json` en la raíz del monorepo con `"strict": true` y las reglas comunes. Cada package/app extiende con `"extends": "../../tsconfig.base.json"` y solo agrega o pisa lo específico (ej: `"outDir"`, `"noEmit"`). Nunca repitas la config en cada package.

**Señal de que lo estás usando bien:** Podés habilitar una regla nueva de TypeScript en un solo archivo (la base) y aplica en todo el monorepo.

---

### TORNILLO #4 — Turbo orquesta respetando el grafo (^build) y cachea solo lo determinístico

**En una línea:** Turborepo corre tasks en el orden correcto según el grafo de dependencias (`^build`) y solo cachea las tareas cuyo output es reproducible.

**La regla para el próximo proyecto:** En `turbo.json`, declarad `"dependsOn": ["^build"]` para las tasks que necesitan que sus dependencias estén compiladas. Excluí del caché las tasks no determinísticas (test con DB real, deploy). Solo cachear lo que produce el mismo output dado el mismo input.

---

### TORNILLO #6 — Cadena onRequest: primero autenticar, después autorizar (y de paso cobrar)

**En una línea:** Los hooks de Fastify se encadenan en orden: autenticación → autorización por permiso → gate de billing; cada uno hace exactamente una cosa y pasa o corta.

**La regla para el próximo proyecto:** En Fastify (o cualquier framework con middleware chain), registrá los hooks en orden explícito: primero el que verifica el JWT y popula request.user (autenticación), después el que chequea el permiso requerido por el endpoint (autorización), y opcionalmente el que verifica el estado de suscripción (billing). Cada hook hace exactamente una cosa y llama next() o responde 401/403/402. No mezcles lógica de autenticación con autorización en el mismo hook.

---

### TORNILLO #7 — Access token en memoria, refresh token persistido y rotado

**En una línea:** El access token vive solo en memoria (nunca en localStorage/cookie sin flags); el refresh token se persiste, se rota en cada uso y se marca como revocado al usarse.

**La regla para el próximo proyecto:** Guardá el access token solo en memoria (variable de módulo o React state), no en localStorage. El refresh token puede ir en una cookie HttpOnly o en storage si es mobile, pero rotalo: al usarlo para obtener un nuevo access token, marcá el refresh viejo como USED/REVOKED en la DB y emití uno nuevo. Si el refresh está revocado, forzá login.

---

### TORNILLO #12 — RLS de Postgres con GUC como red de seguridad de DB (defensa en profundidad)

**En una línea:** Las policies de Row-Level Security usan un GUC (variable de sesión de Postgres) como contexto de tenant, actuando como cuarta capa de defensa detrás del filtro de app.

**La regla para el próximo proyecto:** Si usás Postgres, activá RLS en las tablas de negocio con policies que leen `current_setting('app.tenant_id', true)`. Seteá el GUC al inicio de cada conexión/transacción con el tenant del token. Tratalo como red de seguridad (cuarta capa), no como la única barrera: el filtro en la query de app sigue siendo obligatorio.

---

### TORNILLO #15 — Validar con Zod safeParse antes de tocar la DB

**En una línea:** Toda entrada que llega de HTTP pasa por `schema.safeParse(req.body)` antes del primer acceso a la DB; si falla, se responde 400 con errores por campo.

**La regla para el próximo proyecto:** En el handler, el primer statement después de extraer el body es `const result = schema.safeParse(body)`. Si `!result.success`, respondé 400 con `{ message: 'Datos inválidos', errors: result.error.flatten().fieldErrors }`. Nunca toques la DB con datos no validados.

---

### TORNILLO #16 — El cliente tolera el envelope, no lo asume

**En una línea:** El helper de fetch del frontend desenvuelve tanto `T[]` como `{ data: T[] }` con un helper, sobreviviendo cambios de shape de respuesta.

**La regla para el próximo proyecto:** Si el backend puede devolver arrays directos o envelopes `{data:[]}`, el cliente no asume: usa un helper `unwrap(res)` que detecta si `res.data` existe y lo desenvuelve, o retorna `res` directamente si ya es el array. Así una refactorización del backend no rompe el front.

---

### TORNILLO #17 — Invalidación dirigida de React Query con actualización optimista

**En una línea:** Las mutations invalidan solo las queryKeys afectadas (no todo el caché) y las acciones reversibles parchean el caché de forma optimista antes de confirmar con el servidor.

**La regla para el próximo proyecto:** En cada useMutation, en `onSuccess` invalida solo las queryKeys afectadas (`queryClient.invalidateQueries({ queryKey: ['resource', id] })`). Para acciones reversibles (marcar como leído, toggle), usá `onMutate` + `onError` para aplicar y revertir el cambio optimista.

---

### TORNILLO #21 — RLS como última línea fail-closed (defense in depth en la base)

**En una línea:** Las policies de RLS en Postgres actúan como cuarta capa: si el filtro de app falla, la base bloquea igual; y si el GUC no está seteado, devuelve 0 filas.

**La regla para el próximo proyecto:** Activá RLS con `ALTER TABLE x ENABLE ROW LEVEL SECURITY` y una policy `USING (org_id = current_setting('app.tenant_id')::uuid)`. Testeala como atacante: sin GUC, la query devuelve vacío. Seteá el GUC en cada transacción antes de la primera query.

---

### TORNILLO #22 — Conexiones como singleton con shutdown ordenado, health check y escape hatch a pg crudo

**En una línea:** El cliente de DB es un singleton por proceso con pool configurado, graceful shutdown en SIGTERM/SIGINT, endpoint `/health` que verifica conectividad, y acceso al pg Pool crudo para transacciones manuales.

**La regla para el próximo proyecto:** Instanciá el cliente de DB una sola vez (módulo singleton). Registrá handlers de SIGTERM y SIGINT que llamen a `client.end()` / `pool.end()` antes de salir. Exponé un `/health` que haga un `SELECT 1` y devuelva 200/503. Guardá referencia al pool crudo para los casos que necesiten transacciones manuales con `BEGIN/COMMIT`.

---

### TORNILLO #25 — Validar en la frontera con Zod safeParse y traducir SIEMPRE igual

**En una línea:** Todo endpoint valida con safeParse en la primera línea del handler y responde 400 con el mismo mensaje fijo + errores por campo, sin variaciones de formato.

**Igual que #15 pero con énfasis en la consistencia del mensaje de 400:** El mensaje de error de validación es siempre el mismo string fijo (ej: 'Datos inválidos') más fieldErrors, en todas las rutas sin excepción.

---

### TORNILLO #27 — Refresh silencioso de token en 401 con deduplicación

**En una línea:** Un interceptor de fetch captura los 401, hace el refresh una sola vez (con una Promise compartida para deduplicar), reintenta el request original y limpia la sesión si el refresh falla.

**La regla para el próximo proyecto:** Creá un interceptor de fetch que en 401: verifique que no sea la propia ruta de refresh (evitar loop), inicie el refresh una sola vez guardando la Promise en una variable de módulo (si ya hay una, espera la misma), reintente el request original con el nuevo token, y ante fallo del refresh limpie la sesión y redirija a login. El patrón es idéntico en web y mobile.

---

### TORNILLO #30 — Validación con Zod safeParse y 400 estructurado antes del SQL

**En una línea:** Misma regla que #15 y #25, enfatizando que la validación corre ANTES de cualquier string SQL o llamada al ORM.

---

### TORNILLO #31 — Auto-migración ensureXTable/ensureXColumn en el arranque

**En una línea:** Cada módulo tiene funciones idempotentes `ensureXTable()` / `ensureXColumn()` que crean la tabla o columna si no existe (IF NOT EXISTS), y se llaman al boot de la app.

**La regla para el próximo proyecto:** Para proyectos sin herramienta de migración formal, creá funciones por módulo que hagan `CREATE TABLE IF NOT EXISTS` y `ALTER TABLE ... ADD COLUMN IF NOT EXISTS`, y llamalas en el startup. Cada función es idempotente: correrla dos veces es seguro. El módulo es dueño de su propio schema.

---

### TORNILLO #35 — Fallbacks de env en cascada: DB -> env -> default local

**En una línea:** Los valores de configuración se resuelven en cascada: primero desde la DB (configuración por tenant), luego desde env var, finalmente desde un default hardcodeado seguro.

**La regla para el próximo proyecto:** Para configuración que puede variar por tenant/ambiente, implementá la cascada explícitamente: lee la config de la DB si existe, si no lee la env var, si no usa el default. El default local debe ser seguro para desarrollo (nunca un valor de producción).

---

### TORNILLO #36 — Config de cliente como build-arg, config de servidor como runtime env

**En una línea:** Lo que el browser necesita saber se hornea en el bundle en build time (NEXT_PUBLIC_*, VITE_*); los secretos del servidor se leen en runtime desde env.

**La regla para el próximo proyecto:** Separar estrictamente: variables del cliente (URLs de API, feature flags públicos) van como build args (NEXT_PUBLIC_*, VITE_*). Variables del servidor (secrets, credenciales de DB, API keys) van como runtime env y NUNCA se exponen al bundle del cliente.

---

### TORNILLO #42 — Un solo paquete, tres puertas (barrel @scope/shared)

**En una línea:** El paquete shared exporta su API pública a través de un barrel (index.ts) con tres entry points lógicos: tipos, validaciones y utilidades.

**La regla para el próximo proyecto:** El paquete shared tiene un solo `index.ts` que re-exporta todo lo público. Internamente podés organizar en subcarpetas (types/, schemas/, utils/), pero el consumidor siempre importa desde `@scope/shared`, nunca desde rutas internas del paquete.

---

### TORNILLO #43 — Zod como fuente única: el validador ES el tipo

**En una línea:** Los tipos TypeScript se infieren del schema Zod con `z.infer<typeof schema>`, nunca se definen en paralelo como interface o type separado.

**La regla para el próximo proyecto:** Definí el schema Zod primero, luego `export type X = z.infer<typeof xSchema>`. Nunca mantengas un type/interface por separado que pueda desincronizarse con el schema. El schema es la fuente única de verdad tanto para validación en runtime como para tipos en compile time.

---

### TORNILLO #44 — Permisos como datos: el array const que tipa todo

**En una línea:** Los permisos válidos se definen como un array `as const` en shared, y ese array tipa tanto los guards del backend como los helpers del frontend.

**La regla para el próximo proyecto:** Define `export const PERMISSIONS = ['resource.read', 'resource.manage', ...] as const` en shared. Derivá el tipo `Permission = (typeof PERMISSIONS)[number]`. Usá ese tipo en requirePermission (back) y hasPermission (front). Agregar un permiso nuevo = agregar un string al array.

---

### TORNILLO #45 — El espejo Prisma↔TS: enums de DB = union types de shared

**En una línea:** Los enums definidos en el schema de Prisma se replican como union types en el paquete shared para que el frontend los use sin depender de @prisma/client.

**La regla para el próximo proyecto:** Para cada enum de Prisma que el frontend necesite, definí su equivalente como union type en shared: `export type Status = 'active' | 'inactive' | 'suspended'`. El backend mapea entre el enum de Prisma y el union type al serializar. El frontend solo importa de shared, nunca de @prisma/client.

---

### TORNILLO #47 — setupXRoutes: el módulo es una función auto-instalable

**En una línea:** Cada archivo de routes exporta una función `setupXRoutes(app)` que instala todos sus endpoints al ser llamada, siguiendo el mismo patrón en todo el codebase.

**Igual que #28, con énfasis en la consistencia del nombre:** La convención de nombre `setupXRoutes` (donde X es el dominio: `setupMemberRoutes`, `setupPaymentRoutes`) hace que el bootstrap sea predecible.

---

### TORNILLO #48 — Guards gemelos read/manage al tope del módulo

**En una línea:** Cada módulo de routes define dos constantes de guard al tope: `const readAuth = requirePermission('resource.read')` y `const manageAuth = requirePermission('resource.manage')`, y las reutiliza en cada endpoint.

**La regla para el próximo proyecto:** Al inicio de cada archivo de routes, definí los guards como constantes locales. Los endpoints de lectura usan `readAuth`, los de escritura/eliminación usan `manageAuth`. Esto hace que agregar un endpoint nuevo no requiera recordar el nombre del permiso: está a la vista en las dos primeras líneas del archivo.

---

### TORNILLO #49 — Endpoint CRUD canónico: safeParse + tenant + soft-delete + mapX

**En una línea:** Todo endpoint de escritura sigue el mismo flujo: validar con safeParse → extraer tenant del token → operar en la DB con filtro de tenant → soft-delete en vez de DELETE → mapear la respuesta con una función mapX.

**La regla para el próximo proyecto:** Definí un molde de endpoint que todos los devs copian: (1) safeParse del body, (2) extraer org_id de request.user, (3) operar en DB con WHERE org_id, (4) para borrados setear deleted_at en vez de DELETE, (5) mapear la fila del ORM a la forma de respuesta con una función pura `mapXToResponse(row)`.

---

### TORNILLO #50 — Schema Zod compartido: Create + Update.partial() como fuente de verdad

**En una línea:** El schema de creación se define completo; el de actualización se deriva con `.partial()` para hacer todos los campos opcionales; ambos viven en shared.

**La regla para el próximo proyecto:** Define `const createXSchema = z.object({ ... })` y `const updateXSchema = createXSchema.partial()` en shared. El backend usa ambos para validar; el frontend puede usarlos para tipar formularios. Nunca definas el schema de Create y Update por separado: la consistencia es automática.

---

### TORNILLO #51 — Registrar una vez en index.ts; gatear en Sidebar y hook en mobile

**En una línea:** Cada feature se registra en un solo lugar (index.ts del módulo); la visibilidad en el menú y en la navegación mobile se controla con el mismo modelo de permisos.

**La regla para el próximo proyecto:** El feature registry central (index.ts de la app o del router) es el único lugar donde se "activa" una feature. El Sidebar web y el hook de navegación mobile leen el mismo objeto de registro y filtran por hasPermission. Agregar una feature nueva = una entrada en el registro.

---

### TORNILLO #56 — Fallback por entorno con bandera de origen explícita

**En una línea:** Cuando hay múltiples proveedores de pago (ej: producción vs sandbox), el proveedor se selecciona por env var y la respuesta incluye una bandera `origin` que indica cuál se usó.

**La regla para el próximo proyecto:** Para integraciones con múltiples endpoints/proveedores según el ambiente, usá una env var (PAYMENT_ENV=production|sandbox) y seleccioná el proveedor en un factory. Las respuestas de estas integraciones incluyen un campo `origin` para que logs y tests puedan verificar cuál se usó.

---

### TORNILLO #58 — Constructor incremental de WHERE + params para filtros opcionales

**En una línea:** Cuando un endpoint acepta múltiples filtros opcionales, el WHERE se construye concatenando condiciones y el array de params se va llenando en paralelo, en vez de armar el SQL de una sola vez.

**La regla para el próximo proyecto:** Para queries con filtros opcionales: empezá con `const conditions = ['org_id = $1']` y `const params = [orgId]`. Por cada filtro presente, hacé `conditions.push(\`campo = $\${params.length + 1}\`)` y `params.push(valor)`. Finalizá con `WHERE \${conditions.join(' AND ')}`. Nunca concatenes strings de usuario directamente en el SQL.

**Señal de que lo estás usando bien:** Podés agregar un nuevo filtro opcional en 2 líneas sin tocar el resto de la query; no hay SQL injection posible porque todos los valores van como params.
