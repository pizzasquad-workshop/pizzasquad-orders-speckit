# Tasks 001 — Bulk pricing

> Generado por `/speckit.tasks`. Cada tarea es atomica y mergeable por separado.

- [ ] **T1 · Migracion DB**
  - Crear `prisma/migrations/20260424120000_add_bulk_pricing_to_orders/`.
  - `migration.sql` (up) y `down.sql` (reversible).
  - Aplicar con `pnpm prisma migrate dev`.
  - Acceptance: `pnpm prisma db pull` muestra los nuevos campos.

- [ ] **T2 · Modelo Prisma**
  - Actualizar `prisma/schema.prisma`: añadir `bulkDiscountPct Decimal @default(0)` y `discountAmountCents Int @default(0)` a `Order`.
  - Regenerar cliente: `pnpm prisma generate`.
  - Acceptance: `tsc` compila sin errores.

- [ ] **T3 · Modulo de dominio: tiers**
  - Crear `src/domain/pricing/tiers.ts` con la tabla.
  - Acceptance: tipo exportado y inmutable.

- [ ] **T4 · Modulo de dominio: calculateBulkDiscount**
  - Crear `src/domain/pricing/bulkDiscount.ts`.
  - Funcion pura, devuelve `DiscountResult`.
  - Maneja exclusion de `producto_fresco_premium` y flag `priority=urgent`.

- [ ] **T5 · Tests unitarios**
  - `src/domain/pricing/bulkDiscount.test.ts`.
  - Cubrir: cada tramo, exclusion premium, flag urgent, lista vacia, 0 kg.
  - Acceptance: cobertura del fichero >= 95%.

- [ ] **T6 · Schema zod actualizado**
  - `src/routes/schemas/orders.schemas.ts`: añadir `subtotal`, `discountPct`, `discountAmount`, `total` a `OrderResponseSchema`.

- [ ] **T7 · Repo orders**
  - `src/db/repos/orders.repo.ts`: persistir nuevos campos en `create`.
  - Acceptance: integration test pasa con DB de test.

- [ ] **T8 · Handler POST /orders**
  - Modificar `src/routes/orders.routes.ts` para llamar a `calculateBulkDiscount` y enriquecer respuesta.
  - NO logica de calculo aqui. Solo orquestacion.

- [ ] **T9 · Tests de integracion**
  - `tests/integration/orders.bulkPricing.test.ts`.
  - Caso 1: 250 kg bacalao -> 6%.
  - Caso 2: 250 kg (200 bacalao + 50 langosta) -> 6% sobre 200 kg.
  - Caso 3: pedido urgent 600 kg -> 0%.

- [ ] **T10 · OpenAPI**
  - `docs/openapi.yaml`: bump a 1.1, añadir nuevos campos a `OrderResponse`.

- [ ] **T11 · Bench performance**
  - `vitest --bench` sobre `calculateBulkDiscount` con 100 items.
  - Acceptance: < 1 ms.

- [ ] **T12 · PR final + changelog**
  - Conventional commit: `feat(orders): aplicar descuentos por volumen`.
  - Actualizar `CHANGELOG.md`.
