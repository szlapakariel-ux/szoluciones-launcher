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

Empezá proponiendo la arquitectura técnica y el stack recomendado para este producto.
```
