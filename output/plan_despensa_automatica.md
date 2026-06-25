# Plan: Despensa Automática — Never Out of Stock

## Problema que resuelve
La gente come mal (o pide delivery) porque no tiene alimentos en casa.
Este sistema elimina esa excusa automatizando las compras según el perfil nutricional del hogar.

## Síntesis del producto
Un agente de compras que, dado un perfil de dieta y la cantidad de integrantes del hogar, calcula automáticamente los alimentos necesarios, arma el carrito buscando el mejor precio entre proveedores disponibles, pide confirmación al usuario y coordina la entrega en el horario elegido.
Es una PWA (funciona en móvil y desktop).

---

## Perfiles de usuario (V1)
| Perfil | Descripción |
|---|---|
| Con nutricionista | Sube o carga el plan nutricional mensual |
| Sin nutricionista | Elige un perfil predefinido (equilibrado, bajo en carbos, etc.) |
| Vegano | Perfil predefinido sin productos de origen animal |

---

## Flujo principal
1. **Configuración inicial**
   - El usuario crea su hogar: cantidad de integrantes + perfil nutricional por integrante
   - Elige proveedores habilitados y ventanas horarias de entrega aceptadas

2. **Ciclo semanal (o según frecuencia configurada)**
   - El agente calcula los alimentos faltantes según el plan y el stock estimado
   - Busca precios entre proveedores y arma el carrito de mejor relación precio/calidad
   - Presenta resumen del carrito al usuario para confirmación

3. **Confirmación del carrito**
   - El usuario ve el carrito completo (no producto por producto)
   - Puede ajustar antes de confirmar
   - Confirma → el agente ejecuta la compra y coordina la entrega

4. **Actualización mensual de dieta**
   - Notificación para revisar/actualizar el plan nutricional
   - Compatible con cambios de nutricionista o de perfil

---

## Alcance del MVP
- [ ] Registro de hogar (integrantes + perfiles)
- [ ] Carga de plan nutricional (manual o por perfil predefinido)
- [ ] Cálculo de lista de compras semanal
- [ ] Integración con al menos 1 proveedor de compras online
- [ ] Comparación de precios básica
- [ ] Vista de carrito para confirmación
- [ ] Coordinación de entrega con selección de horario
- [ ] PWA (mobile + desktop)
- [ ] Notificación de renovación mensual de dieta

## Fuera de alcance V1
- Seguimiento nutricional (qué comí, objetivos)
- Límite de presupuesto configurable
- Historial de compras avanzado

---

## Supuestos y riesgos
| Supuesto | Riesgo |
|---|---|
| Los proveedores tienen API o scraping viable | Pueden bloquear o cambiar estructura |
| El usuario actualiza la dieta mensualmente | Sin actualización, el sistema compra lo mismo indefinidamente |
| El stock del hogar se estima (no hay sensor físico) | Puede haber sobrante o faltante real |
| Un carrito por hogar es suficiente en V1 | Hogares con dietas muy diversas pueden necesitar lógica más compleja |

---

## Huecos a resolver antes de ejecutar
1. ¿Qué proveedores online tienen API o permiten integración? (Mercado Libre, Pedidos Ya Market, Disco, Coto, etc.)
2. ¿Cómo se ingresa el plan de la nutricionista? (PDF, formulario manual, foto con OCR)
3. ¿Cómo se estima el stock restante? (consumo promedio, check manual, descuento automático)
4. ¿Modelo de negocio? (suscripción mensual, comisión por compra, freemium)

---

## Tornillos que aplican (del ADN)
Mapeo hecho leyendo el ADN en profundidad. Los 12 son obligatorios para construir sin bugs, fugas ni deuda técnica. Detalle y justificación por tornillo en `prompt_despensa_automatica.md`.

| Tornillo | Por qué aplica al proyecto |
|---|---|
| #9 org_id del JWT | Aislar dieta/integrantes/carritos por hogar |
| #11 Fail-closed sin tenant | No filtrar planes nutricionales entre cuentas |
| #18 Plantilla tabla multi-tenant | Soft-delete e historial uniforme en todas las tablas |
| #15/#25 Validación safeParse | Plan de la nutri y config validados antes de la DB |
| #34/#52 Credenciales cifradas AES-256-GCM | API/login del proveedor que usa el agente |
| #26/#53 Webhook idempotente | Confirmación de compra y entrega sin duplicar |
| #55 Billing gate 402 | Producto comercializable por suscripción |
| #37 Feature flag default apagado | La compra automática gasta plata real |
| #60 Job background con dedupe | El agente de compras corre como cron semanal |
| #57 Paginación con envelope | Catálogo de productos e historial de carritos |
| #23/#24 Errores atajados + envelope español | "Proveedor sin stock" → HTTP claro para la PWA |
| #38/#39 Test integración + caso negativo | Cálculo de lista y flujo de compra con DB real |

Total: **12 tornillos** (mínimo esperado por el PASO 4 del contrato: 8). ✅
