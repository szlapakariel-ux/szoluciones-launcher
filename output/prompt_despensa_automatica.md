# Prompt: Despensa Automática

## Contexto para usar este prompt
Usá este prompt para iniciar la construcción del producto con cualquier agente de desarrollo (Claude Code, Cursor, etc.).

---

## Prompt listo para usar

```
Quiero construir una PWA llamada "Despensa Automática" que resuelve el siguiente problema:
la gente come mal o pide delivery porque no tiene alimentos en casa.
El sistema automatiza las compras según el perfil nutricional del hogar para que eso nunca pase.

### Producto
- PWA funcional en mobile y desktop
- Orientada a ser un producto comercializable con múltiples perfiles de usuario

### Perfiles de usuario V1
1. Con nutricionista: el usuario carga su plan nutricional mensual
2. Sin nutricionista: elige un perfil predefinido (equilibrado, bajo en carbos, etc.)
3. Vegano: perfil predefinido sin productos de origen animal

### Flujo principal
1. El usuario configura su hogar: cantidad de integrantes + perfil nutricional por integrante
2. El sistema calcula semanalmente los alimentos necesarios
3. Un agente busca los mejores precios entre proveedores disponibles y arma el carrito
4. El usuario recibe el carrito completo para confirmar (no producto por producto)
5. Tras confirmación, el agente ejecuta la compra y coordina la entrega según el horario elegido por el usuario
6. Mensualmente, el sistema notifica para revisar/actualizar el plan nutricional

### Restricciones técnicas
- PWA (sin app nativa por ahora)
- El stock del hogar se estima por consumo promedio, no por sensor físico
- Integración inicial con al menos 1 proveedor de compras online

### Lo que NO entra en V1
- Seguimiento nutricional (qué comí, progreso de objetivos)
- Límite de presupuesto configurable
- Historial de compras avanzado

### Preguntas abiertas a resolver antes de implementar
1. ¿Qué proveedor online tiene API o scraping viable para arrancar? (Mercado Libre, Pedidos Ya Market, Disco, Coto)
2. ¿Cómo se ingresa el plan de la nutricionista? (PDF, formulario, foto con OCR)
3. ¿Cómo se estima el stock restante entre compras?
4. ¿Cuál es el modelo de negocio? (suscripción, comisión, freemium)

### TORNILLOS QUE APLICAN (del ADN de SZoluciones)
Mapeados leyendo el ADN en profundidad. Cada uno es obligatorio para construir este proyecto sin bugs, fugas de seguridad ni deuda técnica. (IDs del stack TypeScript: #N; el principio universal equivalente vive en la ferretería maestra U-01..U-38.)

1. **#9 — El org_id sale SIEMPRE del JWT, nunca del cliente.** El `hogar_id` (la cuenta de cada hogar) sale del token verificado, nunca del request. Aísla dieta, integrantes y carritos entre hogares. Ignorarlo = un hogar podría operar con datos de otro.
2. **#11 — Fail-closed: sin contexto de tenant, cero datos.** Si falta el contexto de hogar, no se devuelve nada (no toda la base). Evita fugas de planes nutricionales entre cuentas.
3. **#18 — Plantilla de tabla multi-tenant.** Toda tabla de negocio (dietas, integrantes, productos, carritos, órdenes) lleva id opaco, hogar_id, created_at, updated_at, deleted_at. Da soft-delete e historial uniforme.
4. **#15 / #25 — Validar con Zod safeParse antes de tocar la DB.** El plan de la nutricionista y la config del hogar se validan en la frontera y devuelven 400 con errores por campo antes de escribir. Evita datos de dieta corruptos.
5. **#34 / #52 — Credenciales del proveedor cifradas con AES-256-GCM en reposo.** El login/API key del super con que el agente compra en nombre del usuario nunca se guarda en texto plano.
6. **#26 / #53 — Webhook de pago/proveedor a prueba de duplicados (idempotencia + firma + re-consulta).** La confirmación de compra y el estado de entrega llegan por webhook; se deduplican, se valida firma y se re-consulta al proveedor. Evita comprar o cobrar dos veces.
7. **#55 — Billing de la plataforma como gate de permisos con 402.** Al ser un producto comercializable por suscripción, el acceso a la automatización se gatea por estado de suscripción.
8. **#37 — Feature flags con default apagado para lo caro o riesgoso.** La compra automática gasta plata real: va detrás de un flag por hogar, apagado por default, hasta que el usuario confirme el carrito.
9. **#60 — Jobs en background gateados por secret, con run-log y dedupe idempotente.** El "agente que hace las compras" corre como job semanal: calcula faltantes y arma el carrito. Sin dedupe, un cron disparado dos veces armaría carritos duplicados.
10. **#57 — Paginación con clamp defensivo y envelope { data, total, page, totalPages }.** El catálogo de productos y el historial de carritos se paginan; nunca se trae el catálogo entero.
11. **#23 / #24 — Errores de negocio se atajan, los del sistema explotan; envelope { message } en español.** "Proveedor sin stock" se traduce a HTTP claro; lo inesperado va al handler global. Respuesta uniforme para la PWA.
12. **#38 / #39 — Test de integración con DB real + caso negativo.** Probar el cálculo de la lista y el flujo de compra contra DB real, incluyendo casos negativos (sin stock, dieta vacía, hogar sin integrantes).

(Condicional, si el ingreso del plan se hace con OCR/IA: revisar tornillos Python #32 "proveedor externo caído → fallback degradado" y #68 "guardrails por nivel de riesgo".)

Empezá proponiendo la arquitectura técnica y el stack recomendado para este producto, respetando los tornillos de arriba.
```
