# Spec 001 — Bulk pricing para clientes B2B

**Estado:** Draft
**Owner:** Cova (PM)
**Stakeholders:** Maite (TL), Q (Backend)

## Problema
Los distribuidores grandes (Casa Marina, Pescaderias del Norte, La Lonja Vasca) compran en volumen y nos piden descuentos por cantidad. Hoy lo hacemos manualmente en Excel y se nos olvida en el 30% de los pedidos. Queremos que la API aplique el descuento automaticamente.

## Valor de negocio
- Evita errores manuales (~ 8k €/mes en descuentos olvidados o duplicados).
- Reduce 4 h/semana de trabajo administrativo de Cova.
- Habla a clientes grandes ("pricing transparente").

## Usuario
- **Distribuidor B2B** que crea un pedido via API.
- **Cova (PM)** que define las reglas de descuento desde un panel admin (OUT OF SCOPE en esta spec; v1 hardcodea reglas en config).

## Comportamiento esperado

### Reglas de descuento (v1, hardcoded)
| Cantidad total (kg) | Descuento |
|---------------------|-----------|
| 0 - 49              | 0%        |
| 50 - 199            | 3%        |
| 200 - 499           | 6%        |
| 500+                | 10%       |

### Flujo
1. Cliente envia `POST /orders` con la lista de items.
2. El sistema calcula `total_kg`.
3. El sistema aplica el descuento correspondiente a `unit_price * qty` por linea.
4. La respuesta incluye `subtotal`, `discount_pct`, `discount_amount`, `total`.
5. El descuento queda persistido en `orders.bulk_discount_pct`.

### Reglas de exclusion
- Productos de la categoria `producto_fresco_premium` (langosta, percebe) NO aplican descuento.
- Pedidos urgentes (con flag `priority=urgent`) tampoco.

## Criterios de aceptacion
- [ ] Endpoint `POST /orders` devuelve los nuevos campos (`subtotal`, `discount_pct`, `discount_amount`, `total`).
- [ ] Tests unitarios de la funcion `calculateBulkDiscount(items)` cubren todos los tramos + exclusiones.
- [ ] Test de integracion: pedido de 250 kg de bacalao -> 6% descuento aplicado.
- [ ] Test de integracion: pedido de 250 kg con 50 kg de langosta -> descuento solo sobre 200 kg.
- [ ] OpenAPI actualizada.
- [ ] Performance: el calculo no añade > 5 ms al p95 del endpoint.

## Fuera de scope
- Panel admin para gestionar reglas (sera spec 002).
- Descuentos por cliente (sera spec 003).
- Promociones temporales.

## Riesgos
- Clientes existentes ven cambios en respuestas (`subtotal`/`total` nuevos campos): contratos OpenAPI v1 -> v1.1.
- Migracion: `orders` existentes no tienen `bulk_discount_pct`. Default 0 NOT NULL.
