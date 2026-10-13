# Salida esperada — Mini-flujo {{NUMERO_FLUJO}} — {{NOMBRE_FLUJO}}

> Documento generado en Fase 4. La salida de abajo procede de una **ejecución real** del script
> `run-{{NUMERO_FLUJO}}-{{NOMBRE_FLUJO}}.{{EXT}}` — no está escrita de memoria. Si tu ejecución
> difiere, o el código real ha cambiado o tu entorno difiere del usado aquí: ambas cosas merecen investigarse.
>
> **Driver:** `{{RUTA_DRIVER}}` _(en stacks compilados es un test del proyecto y `run-…` solo lo lanza)_

## Qué código real ejecuta este script

> El script es un **driver delgado**: importa e invoca el código del propio programa.
> Esta tabla es el contrato — si una fila te sorprende, vuelve al doc del mini-flujo en `02-flujos/`.

| Pieza real invocada | Dónde vive | Papel en el script |
|---------------------|------------|--------------------|
| `{{FUNCION_O_HANDLER}}` | `{{FILE:LINE}}` | {{QUE_HACE_AQUI}} |
| `{{FUNCION_O_HANDLER}}` | `{{FILE:LINE}}` | {{QUE_HACE_AQUI}} |

## Fronteras mockeadas (y solo fronteras)

| Frontera | Mock usado | Por qué es frontera | Qué devolvería el sistema real |
|----------|-----------|---------------------|--------------------------------|
| {{DB/API/COLA/RELOJ}} | `{{LIB_Y_OBJETO}}` | {{RAZON}} | {{COMPORTAMIENTO_REAL}} |

_Todo lo que no aparece en esta tabla corre con el código real del programa._

## Configuración neutralizada

> El driver sobrescribe esta configuración antes de arrancar el código, para que nada toque sistemas reales.
> Los ficheros de configuración del proyecto no se modifican.

| Origen | Clave / variable | Valor real (resumen, sin secretos) | Valor en el driver |
|--------|------------------|------------------------------------|--------------------|
| `{{.env / appsettings / application.yml}}` | `{{CLAVE}}` | {{QUE_APUNTARIA}} | `{{VALOR_INOCUO}}` |

## Piezas reutilizadas del repo

_Fixtures, factories, seeds, helpers de test o docker-compose existentes que el script aprovecha en lugar de crear mocks nuevos. Si esta lista está vacía, justifica por qué._

- {{PIEZA_REUTILIZADA}} — `{{RUTA}}`

## Cómo ejecutarlo

```{{SHELL}}
{{COMANDOS_DE_EJECUCION}}
```

_Las dependencias son las del propio programa ({{FICHERO_DEPS}}); el script no instala nada aparte._

## Salida obtenida en la ejecución real

> Fecha de ejecución: {{FECHA}} · Entorno: {{ENTORNO_BREVE}}

```{{LENGUAJE_FORMATO}}
{{SALIDA_REAL_CAPTURADA}}
```

## Cómo interpretar la salida

{{EXPLICACION_LINEA_A_LINEA_O_POR_BLOQUES}}

## Diferencias con la salida aproximada de la Fase 2

_Compara con la "Salida aproximada" de `02-flujos/{{NUMERO_FLUJO}}-{{NOMBRE_FLUJO}}.md`. Si coinciden, dilo en una línea; si no, explica qué difiere y por qué (el documento del mini-flujo ya se ha corregido)._

{{DIFERENCIAS_O_COINCIDE}}

## Variantes que puedes probar

_Cambios al dataset `{{NUMERO_FLUJO}}-{{NOMBRE_FLUJO}}-input.json` y qué deberías observar._

- {{VARIANTE_1}} → {{EFECTO_ESPERADO}}
- {{VARIANTE_2}} → {{EFECTO_ESPERADO}}
