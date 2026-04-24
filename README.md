# pizzasquad-orders-speckit

**Demo del Modulo 5: Spec-Driven Development con Spec Kit.**

Este seed muestra el ciclo completo `constitution -> spec -> plan -> tasks -> implement` usando el toolkit oficial [github/spec-kit](https://github.com/github/spec-kit) sobre una feature realista de Pizza Squad platform: **descuentos por volumen para clientes B2B**.

## Estructura

```
.specify/
  memory/
    constitution.md                          # Principios inviolables del proyecto
  specs/
    001-add-bulk-pricing/
      spec.md     # QUE construir (user value, no implementacion)
      plan.md     # COMO (arquitectura, modulos, contratos)
      tasks.md    # Lista granular accionable
```

## Los 5 slash commands

| Comando | Que hace | Cuando |
|---------|----------|--------|
| `/speckit.constitution` | Genera/actualiza `memory/constitution.md` con los principios. | Al iniciar el proyecto. Una vez. |
| `/speckit.specify` | Convierte un brief en `spec.md` (qué + por qué + criterios de aceptacion). | Cada feature. |
| `/speckit.plan` | Genera `plan.md` (arquitectura, modulos, contratos). | Tras revisar la spec. |
| `/speckit.tasks` | Descompone el plan en `tasks.md` granular. | Tras revisar el plan. |
| `/speckit.implement` | Ejecuta tarea a tarea, abriendo PRs. Pide feedback humano entre tareas. | Cuando todo lo anterior esta validado. |

## Como reproducir el demo

1. Instala spec-kit: `uvx spec-kit init` (no necesario aqui, ya esta inicializado).
2. Abre VSCode con Copilot Chat (Agent mode).
3. Lee la spec con el equipo: ¿esta claro el valor? ¿faltan criterios?
4. Lanza `/speckit.plan` y observa como genera `plan.md`. Compara con el fichero ya commiteado.
5. Lanza `/speckit.tasks` y observa como descompone.
6. Opcional: `/speckit.implement` ejecuta T1, T2, ... abriendo PRs.

## Por que esto importa

Sin spec-kit, el agente:
- Se inventa el problema o lo entiende a medias.
- Salta a codigo sin pensar arquitectura.
- Hace PRs gigantes que mezclan refactor, feature y bug.

Con spec-kit:
- Hay artefacto humano (spec.md) que define el QUE.
- Hay artefacto auditable (plan.md) que define el COMO.
- El plan se descompone en PRs pequeños revisables.
- La constitution rechaza decisiones que rompen principios.

## Recursos

- https://github.com/github/spec-kit
- https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/
- Modulo 5 del manual.
