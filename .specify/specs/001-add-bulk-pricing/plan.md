# Plan 001 — Bulk pricing

> Generado por `/speckit.plan` a partir de `spec.md` + `constitution.md`. Revisado por Maite.

## Arquitectura

```
src/
  domain/
    pricing/
      bulkDiscount.ts        # funcion pura calculateBulkDiscount
      bulkDiscount.test.ts   # unit tests
      tiers.ts               # tabla de tramos (configurable a futuro)
  routes/
    schemas/
      orders.schemas.ts      # zod schema de OrderCreate y OrderResponse (con nuevos campos)
    orders.routes.ts         # handler POST /orders modificado
  db/
    repos/
      orders.repo.ts         # save con bulk_discount_pct
prisma/
  migrations/
    20260424120000_add_bulk_pricing_to_orders/
      migration.sql
      down.sql
docs/
  openapi.yaml               # bump a v1.1
```

## Modulos y contratos

### `domain/pricing/bulkDiscount.ts`
```ts
type Item = { sku: string; qty: number; unitPrice: number; category: string }
type DiscountResult = { subtotal: number; discountPct: number; discountAmount: number; total: number }

export function calculateBulkDiscount(items: Item[], priority: 'normal'|'urgent'): DiscountResult
```
- Pure function, sin IO.
- Excluye items con `category === 'producto_fresco_premium'` del calculo de kg.
- Si `priority === 'urgent'`, devuelve `discountPct = 0`.

### `domain/pricing/tiers.ts`
```ts
export const TIERS = [
  { minKg: 0,   pct: 0 },
  { minKg: 50,  pct: 3 },
  { minKg: 200, pct: 6 },
  { minKg: 500, pct: 10 },
] as const
```

### `routes/orders.routes.ts`
- Tras validar el body, llama a `calculateBulkDiscount`.
- Persiste con el repo y devuelve la respuesta enriquecida.

## Migracion

```sql
-- up
ALTER TABLE orders ADD COLUMN bulk_discount_pct NUMERIC(5,2) DEFAULT 0 NOT NULL;
ALTER TABLE orders ADD COLUMN discount_amount_cents INTEGER DEFAULT 0 NOT NULL;

-- down
ALTER TABLE orders DROP COLUMN IF EXISTS discount_amount_cents;
ALTER TABLE orders DROP COLUMN IF EXISTS bulk_discount_pct;
```

## Riesgos y mitigacion
- **Contrato roto:** clientes existentes pueden no esperar campos nuevos. Mitigacion: campos OPCIONALES en OpenAPI v1 + obligatorios en v1.1.
- **Performance:** el calculo es O(n) sobre items. n < 50 tipico. Sin riesgo.
- **Floats:** todo en centimos (integers) en DB. Conversion en boundary.

## Validacion
- Constitution check: tests primero, tipado estricto, migracion reversible. OK.
- Performance budget: < 5 ms añadidos al p95. Validar con `vitest --bench`.
