---
fase_actual: 1  # 1 | 2 | 2.2 | 3 | 4
estado_fase: en_curso  # en_curso | completada
# Cada mini-flujo se registra con su número estable, p. ej. "01-nombre-del-flujo".
# El número se asigna al identificarlos y NO cambia aunque se exploren en otro orden.
mini_flujos_identificados: []
mini_flujos_completados: []
mini_flujos_pendientes: []   # elegidos por el usuario y aún sin documentar
mini_flujos_descartados: []  # elegidos y luego descartados con confirmación expresa del usuario
mini_flujos_profundizados: []  # los que pasaron por Fase 2.2
scripts_pendientes: []   # Fase 4: flujos elegidos para script y aún sin ejecutar
scripts_completados: []  # Fase 4: scripts generados y ejecutados
ultimo_paso: "Inicio del onboarding"
fecha_creacion: AAAA-MM-DD
fecha_actualizacion: AAAA-MM-DD
ruta_repo: ""
commit_analizado: ""  # `git rev-parse --short HEAD` al hacer la Fase 1; las referencias file:line apuntan a este commit
stack_detectado: ""
nivel_detalle: ""  # resumen_ejecutivo | detallado_tecnico
dudas_iniciales: []  # funcionalidades o dudas que el usuario mencionó en la Fase 1
docs_en_gitignore: false  # true si el usuario pidió no versionar docs/onboarding/
nivel_tecnologias:
  # Tecnologías que el usuario eligió que le explicaran de paso.
  # Si la lista está vacía, asumimos que domina todo el stack.
  - tecnologia: ""
    explicada_en: "01-vision-general.md"  # dónde se documentó
---

# Estado del onboarding

> Este archivo es el **estado persistente** del proceso. Claude lo lee al inicio de cada turno y lo actualiza al final. No lo edites a mano salvo que quieras forzar un punto del recorrido.

## Histórico

- _AAAA-MM-DD_ — Onboarding iniciado.

## Notas del usuario

_(Sección libre — el usuario puede dejar aquí anotaciones que Claude tendrá en cuenta en el próximo turno)._
