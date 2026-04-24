# Constitution — Pizza Squad platform v1.0

> Estos principios son INVIOLABLES. Cualquier `/speckit.plan` o `/speckit.implement` que los rompa debe ser rechazado.

## 1. Tests primero
- Cobertura minima 80% en `src/domain/**`.
- Todo PR sin tests se rechaza, ni siquiera de un agente.
- Tests de integracion para todo endpoint nuevo.

## 2. Tipado estricto
- TypeScript strict mode obligatorio.
- Cero `any`, `as any`, `@ts-ignore` sin comentario justificando.
- Errores tipados con `Result<T, E>` (neverthrow). Nunca `throw` en handlers ni dominio.

## 3. Migraciones reversibles
- Cada migracion Prisma tiene su `down`.
- Nunca modificar migraciones aplicadas. Crear nueva.
- Drops de columna en deploy posterior al que deja de usarla.

## 4. Seguridad
- Cero secretos en el repo. Solo `.env.example`.
- Toda entrada HTTP validada con `zod` en boundary.
- Rate-limit (100 req/min por API key) en todos los endpoints publicos.
- Logs nunca incluyen PII (emails, telefonos sin hash).

## 5. Tamaño y scope
- PRs < 400 LOC. Divide si excede.
- Una feature por PR. Refactors separados.
- Conventional commits con scope: `feat(orders): ...`.

## 6. Performance budget
- p95 endpoint listas < 200 ms.
- p95 endpoint detalle < 80 ms.
- Cero N+1 queries: si Prisma genera mas de una query por entidad, refactoriza.

## 7. Documentacion viva
- Si tocas API publica, actualiza `docs/openapi.yaml` en el MISMO PR.
- ADRs en `docs/adr/` para cualquier decision arquitectonica.

## 8. Dependencias
- No introducir dependencias nuevas sin justificarlo en el PR.
- Mantener `pnpm audit` limpio.
