# Prompt listo para usar — idea de dieta

## Contexto
Se quiere construir un sistema para llevar el seguimiento de una dieta personal. El usuario necesita registrar alimentos, controlar metas y recibir recordatorios simples sin complicaciones técnicas.

## Objetivo
Desarrollar una primera versión que permita:
- registrar comidas por día,
- guardar metas de consumo,
- revisar el progreso de forma simple,
- y preparar recordatorios básicos.

## Requisitos
- El flujo debe ser claro y directo para un usuario no técnico.
- Debe haber una separación entre la lógica de negocio y la capa de acceso a datos.
- Debe haber validaciones básicas y pruebas de humo.

## TORNILLOS QUE APLICAN
- Tornillo #11 — Fail-closed: sin contexto de tenant, cero datos. Aplica porque las dietas y metas deben quedar protegidas por usuario; si falta contexto válido, no se deben exponer datos.
- Tornillo #33 — El .env.example como contrato narrado, no como lista muerta. Aplica porque el sistema necesitará variables para notificaciones, email o integraciones externas.
- Tornillo #41 — QA por smoke tests contra entorno real, gateando el release. Aplica porque la versión inicial debe validar al menos el registro de comidas, la carga de metas y el flujo de recordatorios.
- Tornillo #29 — Fail-closed multi-tenant: nunca una query sin org_id + soft delete. Aplica con adaptación para el caso de usuario/espacio personal: toda consulta debe respetar el scope del usuario y no exponer datos ajenos.

## PROTOTIPOS PENDIENTES
- PX-01: definir si la app será solo personal o si luego podrá compartir datos con un nutricionista.
- PX-02: decidir la lógica de recordatorios: notificaciones, emails o solo alertas en pantalla.
- PX-03: definir si habrá soporte para recetas, escaneo de alimentos o solo registro manual.

## Instrucción para el agente de desarrollo
Antes de escribir código, leé los archivos de ADN del launcher: [ADN/szoluciones_ferreteria_maestra.md](../ADN/szoluciones_ferreteria_maestra.md), [ADN/szoluciones_tornillos_universales.md](../ADN/szoluciones_tornillos_universales.md), [ADN/szoluciones_tornillos_typescript.md](../ADN/szoluciones_tornillos_typescript.md) y [ADN/szoluciones_tornillos_python.md](../ADN/szoluciones_tornillos_python.md). Usá esos tornillos como base para las decisiones de implementación y no ignores los huecos que queden sin cubrir.
