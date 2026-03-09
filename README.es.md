# triggers

[🇺🇸 English](README.md)

Un repositorio de prueba para disparadores de flujos de trabajo fallidos — específicamente para probar el patrón en el que un flujo de trabajo de CI falla y un agente de Copilot se activa automáticamente para analizar y corregir el fallo.

## Flujos de trabajo

| Flujo de trabajo | Archivo | Descripción |
|-----------------|---------|-------------|
| **Verificar comentarios de código** | `.github/workflows/check-comments.yaml` | Analiza los archivos fuente en busca de comentarios `// BROKEN`, `// FIXME`, `// HACK` y falla si se encuentran |
| **Compilación** | `.github/workflows/build.yaml` | Ejecuta `npm ci` y `tsc` en el directorio `app/`; falla ante errores de compilación de TypeScript |

## Agentes

| Agente | Archivo | Disparador |
|--------|---------|------------|
| **Reparación de CI** | `.github/agents/ci-repair.md` | `workflow_run: failed` en los flujos de trabajo Verificar comentarios de código y Compilación |
| **Clasificador** | `.github/agents/triager.md` | `issues: opened` |

## Cómo provocar un fallo en el flujo de trabajo

### Método 1: Comentario prohibido (el más sencillo)

Agrega un comentario prohibido en cualquier archivo `.ts` o `.js` y haz push:

```typescript
// HACK esto necesita ser refactorizado
```

El flujo de trabajo **Verificar comentarios de código** lo detectará y fallará, activando el agente de Reparación de CI.

### Método 2: Error de compilación de TypeScript

Introduce un error de tipo en `app/src/index.ts` y haz push:

```typescript
const port: number = "no es un número"; // error de tipo
```

El flujo de trabajo de **Compilación** fallará en `tsc`, activando el agente de Reparación de CI.

### Método 3: Ejecución manual

Ambos flujos de trabajo admiten `workflow_dispatch` — ejecútalos manualmente desde la pestaña Actions.
