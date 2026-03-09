# triggers

[🇺🇸 Read in English](README.md)

Un repositorio de prueba para activadores de flujos de trabajo fallidos — específicamente para probar el patrón en el que un flujo de trabajo de CI falla y un agente de Copilot se activa automáticamente para analizar y corregir el fallo.

## Flujos de trabajo

| Flujo de trabajo | Archivo | Descripción |
|------------------|---------|-------------|
| **Verificar comentarios de código** | `.github/workflows/check-comments.yaml` | Analiza los archivos fuente en busca de comentarios `// BROKEN`, `// FIXME`, `// HACK` y falla si se encuentra alguno |
| **Compilar** | `.github/workflows/build.yaml` | Ejecuta `npm ci` y `tsc` en el directorio `app/`; falla en errores de compilación de TypeScript |

## Agentes

| Agente | Archivo | Activador |
|--------|---------|-----------|
| **Reparación de CI** | `.github/agents/ci-repair.md` | `workflow_run: failed` en los flujos de trabajo de Verificar comentarios de código y Compilar |
| **Clasificador** | `.github/agents/triager.md` | `issues: opened` |

## Cómo activar un fallo en el flujo de trabajo

### Método 1: Comentario prohibido (más fácil)

Agrega un comentario prohibido a cualquier archivo `.ts` o `.js` y haz un push:

```typescript
// HACK esto necesita ser refactorizado
```

El flujo de trabajo **Verificar comentarios de código** lo detectará y fallará, activando el agente de Reparación de CI.

### Método 2: Error de compilación de TypeScript

Introduce un error de tipo en `app/src/index.ts` y haz un push:

```typescript
const port: number = "not a number"; // error de tipo
```

El flujo de trabajo **Compilar** fallará en `tsc`, activando el agente de Reparación de CI.

### Método 3: Despacho manual

Ambos flujos de trabajo admiten `workflow_dispatch` — actívalos manualmente desde la pestaña Actions.
