---
name: onboard-program
description: Guía interactiva paso a paso para que un desarrollador nuevo entienda un programa, servicio o repositorio que no conoce — de lo general a lo específico, con documentación, diagramas y scripts ejecutables con datos mockeados al final. ACTIVA SIEMPRE esta skill cuando el usuario quiera comprender, explorar, hacer onboarding o conocer el funcionamiento de un programa desconocido, aunque no diga la palabra "onboarding", y también cuando quiera retomar un onboarding en curso (existe `docs/onboarding/_progress.md`). Frases típicas que la activan ⇒ "quiero entender este programa", "onboarding de este repo", "explícame este código paso a paso", "preséntame este servicio", "ayúdame a comprender qué hace este código".
---

# Onboard Program — Guía interactiva de comprensión

Esta skill convierte la pregunta "no entiendo este programa" en un recorrido pedagógico de 4 fases: visión general → mini-flujos → repaso → implementación con mocks. El dev nuevo termina con documentación, diagramas, checklist y scripts ejecutables que prueban cada flujo con datos sintéticos.

> **Rutas de plantillas.** Toda ruta `templates/…` de este documento es relativa al directorio de esta skill (la carpeta que contiene este `SKILL.md`), **no** al repositorio analizado. Si el repo analizado tiene su propia carpeta `templates/`, ignórala a estos efectos.

## El principio que nunca debes olvidar

**Dialoga, no resuelvas.** El error más fácil al usar esta skill es contestar al usuario con un único mensaje gigante que cubra las 4 fases. Si haces eso, la skill ha fallado, aunque la documentación sea técnicamente correcta.

Razones:

1. **Cognición humana.** Un dev nuevo no asimila 4 fases de información seguidas; las olvida.
2. **Personalización.** Solo sabrás qué mini-flujos le interesan, o si quiere drill-down, si le preguntas entre fases.
3. **Checklist vivo.** El checklist solo cobra sentido si el usuario lo marca a medida que avanza — eso requiere ritmo conversacional.

Por eso, **al final de cada fase**, párate y pregunta al usuario qué quiere hacer a continuación. Sin excepciones.

### Cómo preguntar

- **Elecciones de 2 a 4 opciones** (seguir o profundizar, nivel de detalle, confirmaciones…) → usa `AskUserQuestion`.
- **Elecciones entre más de 4 opciones** (tecnologías a explicar, mini-flujos a explorar, scripts a generar) → **no uses `AskUserQuestion`**: solo admite entre 2 y 4 opciones por pregunta y recortaría la lista. Muestra la lista **numerada en texto**, termina tu turno y pide que el usuario responda con los números (`1, 3, 7`), rangos (`2-5`), `todos` o `ninguno`. Si la respuesta es ambigua, repítele tu interpretación antes de continuar.

## Fase 0 — Recepción (siempre primero)

Antes de hacer cualquier cosa:

1. **Determina qué programa se analiza.** Si el usuario no lo ha dicho y el directorio de trabajo no es claramente el repo en cuestión, pregúntale la ruta antes de continuar.
2. Comprueba si existe `docs/onboarding/_progress.md` en ese repo.
3. **Si existe** → léelo, identifica `fase_actual` y retoma desde ahí. No repitas fases completadas ni vuelvas a preguntar decisiones ya registradas. Compara `commit_analizado` con el commit actual (`git rev-parse --short HEAD`): si difieren, avisa al usuario de que las referencias `file:line` pueden haberse desplazado y pregúntale si quiere seguir igualmente o refrescar los documentos afectados.
4. **Si no existe** → arrancas desde la Fase 1.

Sin esta lectura inicial, cada turno reinicia el proceso y se pierde la continuidad. El estado en disco es lo que mantiene viva la conversación entre sesiones.

## Fase 1 — Visión general

**Objetivo:** que el dev entienda en 5–10 minutos qué hace el programa, qué no hace, y cómo está estructurado.

**Pasos:**

1. Usa `AskUserQuestion` (una sola llamada, varias preguntas) para acotar el alcance:
   - "¿Qué nivel quieres en esta fase: resumen ejecutivo o detallado técnico?"
   - "¿Hay alguna funcionalidad o duda concreta que ya te preocupe?" (opciones del tipo "No, empieza por lo general" / "Sí, te la escribo" — la respuesta libre llega por "Other")
   - "La documentación se generará en `docs/onboarding/` dentro del repo. ¿Quieres versionarla en git o que la añada a `.gitignore`?"

   Si eligió `.gitignore`, añade la línea `docs/onboarding/` al `.gitignore` del repo (créalo si no existe) antes de escribir nada.

2. Analiza el repositorio:
   - Detecta el stack (lee `package.json`, `pom.xml`, `*.csproj`, `requirements.txt`, `go.mod`, etc.)
   - Localiza entry points (`main.py`, `index.ts`, `Program.cs`, controllers, comandos CLI)
   - Lee el `README` si existe
   - Mapea la estructura de carpetas a alto nivel
   - Anota el commit analizado (`git rev-parse --short HEAD`); si el repo no usa git, anota la fecha.

3. **Calibra el nivel del usuario sobre las tecnologías clave.** Una vez detectado el stack, identifica las tecnologías nucleares del programa (típicamente 4–8: lenguaje, framework, broker, base de datos, cache, herramientas notables) y muéstraselas como **lista numerada en texto** (ver "Cómo preguntar"):
   > "He detectado estas tecnologías clave en el programa: **1.** … **2.** … **3.** … ¿De cuáles quieres que te explique los conceptos básicos de paso? Respóndeme con los números (por ejemplo `1, 4`), o `ninguna` si las dominas todas."

   Termina el turno aquí y espera su respuesta.

   **Por qué importa:** un dev nuevo puede estar cómodo con FastAPI pero no haber tocado nunca InfluxDB. Sin esta calibración, o aburres al senior con explicaciones que ya conoce, o dejas al junior perdido en jerga sin avisar. La skill se adapta al humano que tiene delante.

4. Genera `docs/onboarding/01-vision-general.md` siguiendo `templates/01-vision-general.md`. Debe incluir:
   - **Commit analizado** en la cabecera
   - **Qué hace** (1 párrafo)
   - **Qué NO hace** (alcance y no-alcance — esta sección evita expectativas erróneas; es la que más fricción ahorra)
   - **Cómo lo hace** (stack + arquitectura)
   - **Flujo principal** (diagrama Mermaid de alto nivel)
   - **Tecnologías que vas a aprender de paso** — añade esta sección **solo si el usuario marcó tecnologías en el paso 3**. Por cada tecnología seleccionada incluye: qué es (1 párrafo), para qué se usa EN ESTE programa concretamente, 3–5 conceptos mínimos que conviene conocer con definición breve, y 1–2 enlaces a recursos para profundizar.
   - **Glosario rápido** (siempre): adapta el detalle al nivel calibrado. Si el usuario no marcó ninguna tecnología, incluye solo términos específicos del programa (sus dominios, sus nombres internos). Si marcó tecnologías, añade también términos técnicos generales relacionados con esas tecnologías.

5. Crea `_progress.md` a partir de `templates/_progress.md` (o actualízalo) marcando la Fase 1 como completada. Registra `ruta_repo`, `commit_analizado`, `stack_detectado`, `nivel_detalle`, `dudas_iniciales`, `docs_en_gitignore` y `nivel_tecnologias` (las tecnologías que se explicaron de paso). Esta información se usará en fases posteriores (por ejemplo, al documentar mini-flujos, evitar repetir explicaciones ya dadas, o priorizar las dudas iniciales).

6. Crea `checklist.md` a partir de `templates/checklist.md`. Si hubo tecnologías explicadas, inserta en el marcador `<!-- placeholder:tecnologias -->` una entrada `- [ ] Tecnología X comprendida` por cada una.

7. **Párate** y pregunta con `AskUserQuestion`:
   > "He generado la visión general en `01-vision-general.md`. ¿Pasamos a desglosar mini-flujos (Fase 2), o quieres que profundice antes en algún punto?"

## Fase 2 — Mini-flujos

**Objetivo:** que el dev entienda los flujos funcionales del programa, uno a uno, eligiendo los que más le interesan.

**Pasos:**

1. **Identifica TODOS los mini-flujos posibles** del programa, no una muestra. Un mini-flujo es una secuencia coherente entrada → proceso → salida. Típicamente uno por:
   - Endpoint HTTP
   - Comando CLI
   - Caso de uso de negocio
   - Trabajo programado (job, cron)
   - Evento consumido (mensaje en cola, webhook)

   Suelen aparecer entre 5 y 20. Lístalos todos, aunque parezcan menores.

   **Numéralos siempre.** En cuanto identifiques los mini-flujos, asígnales un número correlativo estable (`1`, `2`, `3`…) y úsalo de forma consistente en TODO el onboarding: en la lista que muestras al usuario, en `_progress.md`, en el nombre de fichero (`02-flujos/NN-<nombre>.md` con dos dígitos: `01`, `02`…), en el título del documento, en el checklist y en el repaso. El número se fija una vez y no cambia aunque el usuario elija explorarlos en otro orden o descarte algunos — así el flujo `07` se llama siempre `07` en cualquier sitio donde aparezca.

   Registra la lista completa en `mini_flujos_identificados` de `_progress.md`.

2. Presenta la lista **numerada en texto** (no con `AskUserQuestion`: casi siempre habrá más de 4 opciones), con una línea de descripción por flujo, y termina el turno:
   > "He identificado N mini-flujos: **1.** … **2.** … **3.** … ¿Cuáles quieres explorar? Respóndeme con los números (`1, 3, 7`), rangos (`2-5`) o `todos`. Los documentaré en ciclos de hasta 4, con una parada entre ciclos para que puedas redirigir."

   Si el usuario mencionó dudas concretas en la Fase 1, señala qué flujos las cubren.

   Registra todos los elegidos en `mini_flujos_pendientes` de `_progress.md`, **conservando su número**. Esa lista es el contrato de completitud: la Fase 2 no termina mientras quede algo ahí.

3. Procesa los mini-flujos elegidos **en ciclos de hasta 4**, respetando el orden seleccionado. Por cada flujo del ciclo:

   a. Genera `docs/onboarding/02-flujos/NN-<nombre>.md` (donde `NN` es el número del mini-flujo a dos dígitos) siguiendo `templates/02-flujo.md`. El título del documento empieza por ese mismo número. Debe contener siempre:
      - Descripción del flujo (qué problema resuelve)
      - Diagrama de secuencia (Mermaid)
      - Referencias `file:line` a cada paso clave
      - **Datos mockeados** de entrada (JSON/CSV/lo que aplique)
      - **Salida aproximada**, deducida leyendo el código y marcada como tal: no se ha ejecutado nada todavía, y se verificará en la Fase 4
      - Cómo ejecutarlo manualmente o con script

      Y **cuatro secciones opcionales** que enriquecen el documento cuando el flujo lo merece. El criterio detallado está en los comentarios HTML del template (lee `templates/02-flujo.md` antes de generar); resumen:

      - **"Cuándo ocurre"** — inclúyela si el contexto del despliegue no es obvio (escenarios específicos, dispositivos especiales, disparadores no triviales). Omítela si el flujo siempre se ejecuta igual.
      - **"Por qué este camino"** — inclúyela si hay decisiones de diseño no triviales (workers async, retries, dedup, DLQs, backpressure, caches…). Omítela si el flujo es directo.
      - **"Conceptos clave"** (tabla glosario) — inclúyela si el flujo introduce **≥3 términos técnicos** no explicados aún en otros docs del onboarding. **Para evaluar este criterio, consulta `mini_flujos_completados` en `_progress.md`** y los conceptos cubiertos en la Fase 1 (§5 Tecnologías). La tabla tiene 4 columnas: Concepto / Qué es / **Analogía cercana** / Rol en este flujo. **La analogía no es opcional dentro de la tabla**: cada definición técnica va acompañada de una comparación con algo cotidiano que el dev probablemente ya conoce (una boda, una biblioteca, un ascensor, una agenda…). Sin analogía, la definición es solo otra forma de jerga.
      - **"Ejemplo concreto"** — inclúyela si el flujo es abstracto o tiene varios pasos que cuesta visualizar en frío. Cuenta UNA historia concreta —un valor real, un dispositivo concreto, un caso de uso— que recorra el flujo de principio a fin con números reales, anclando los conceptos a algo tangible. Suele ir muy bien justo antes del diagrama de secuencia. Omítela si los pasos del flujo ya son suficientemente concretos por sí mismos.

      Estas cuatro secciones existen para que un dev nuevo no tropiece con jerga sin definir, pero **inflar todos los flujos con ellas es contraproducente** (un flujo trivial como `health-metrics` no las necesita). Decide caso a caso: incluye una sección solo si puedes rellenarla con contenido específico de este flujo; si solo te sale texto genérico, omítela. La única excepción es "Conceptos clave": ante la duda, inclúyela, porque la jerga sin definir es el fallo más caro del onboarding.

   b. Actualiza `_progress.md`: mueve el flujo de `mini_flujos_pendientes` a `mini_flujos_completados`.

   c. Inserta en el marcador `<!-- placeholder:mini_flujos -->` del `checklist.md` una entrada `- [ ] Mini-flujo NN '<nombre>' comprendido` (con el número del flujo).

   Al cerrar el ciclo (4 flujos, o menos si ya no quedan más pendientes), **párate** con `AskUserQuestion` mostrando el progreso de forma explícita:
   > "Ciclo completado: he documentado [lista del ciclo]. Llevas X de N; quedan pendientes: [lista]. ¿Seguimos con el siguiente ciclo, profundizamos alguno (Fase 2.2), o pasamos a la Fase 3?"

   **Garantía de completitud (regla dura):** no consideres la Fase 2 terminada mientras `mini_flujos_pendientes` no esté vacía. Si el usuario pide saltar a la Fase 3 con flujos aún pendientes, no los descartes en silencio: confírmalo de forma expresa —"Quedan sin documentar: [lista]. ¿Los descartas explícitamente?"— y solo avanza tras su confirmación. Los flujos descartados así pasan de `mini_flujos_pendientes` a `mini_flujos_descartados`. Ningún flujo seleccionado se omite sin orden directa del usuario.

### Fase 2.2 — Drill-down opcional

Solo si el usuario lo pide explícitamente. Mientras dure, `fase_actual` vale `2.2`; al terminar vuelve a `2` (o avanza a `3` si el usuario lo decide). Para el mini-flujo seleccionado:

1. Genera `docs/onboarding/02-flujos/NN-<nombre>-detalle.md` (mismo número que el mini-flujo) siguiendo `templates/02-flujo-detalle.md`:
   - **Funciones clave**: firma, responsabilidad, complejidad ciclomática estimada
   - **Ficheros implicados**: lista con `file:line` por cada paso del flujo
   - **Flujos transversales**: cómo cruzan el mini-flujo el logging, la autenticación, el manejo de errores, la persistencia

2. Añade el flujo a `mini_flujos_profundizados` en `_progress.md` e inserta en el marcador `<!-- placeholder:drill_down -->` del checklist una entrada `- [ ] Drill-down NN '<nombre>' comprendido`.

3. **Párate** y pregunta si vuelve a Fase 2 (otro mini-flujo) o pasa a Fase 3.

## Fase 3 — Repaso de flujos importantes

**Objetivo:** consolidar comprensión antes de la implementación final.

**Pasos:**

1. Genera `docs/onboarding/03-repaso.md` siguiendo `templates/03-repaso.md`:
   - Lista consolidada de los mini-flujos cubiertos
   - **Dependencias** entre ellos (qué flujo llama a qué flujo)
   - **Patrones repetidos** (validación de entrada, persistencia, eventos, retry)
   - **Riesgos comunes** detectados (efectos secundarios, race conditions, lugares frágiles)

2. Actualiza `_progress.md` con Fase 3 completada.

3. **Párate**:
   > "Repaso listo. ¿Pasamos a la implementación con datos mockeados (Fase 4)?"

## Fase 4 — Implementación con datos mockeados

**Objetivo:** que el dev pueda **ejecutar** los flujos con datos sintéticos y ver el resultado real, no solo leerlo. Y "real" significa real: el script ejecuta **el código del propio programa**, no una recreación.

**El principio rector: mock mínimo, código real máximo.** El valor pedagógico está en ver correr el código verdadero —el mismo que el dev leyó en la Fase 2, con sus `file:line`— alimentado con datos sintéticos. Un script que reimplementa la lógica del flujo no enseña el programa; enseña tu imitación del programa, y se desactualiza al primer commit. Por eso:

- **El script es un driver delgado**: importa los módulos/funciones/handlers reales del programa y los invoca. Su único contenido propio son la carga del dataset, el arranque de los mocks de frontera y la impresión del resultado.
- **Mockea solo las fronteras de E/S**: red, base de datos, colas, reloj, aleatoriedad, sistema de ficheros externo. Todo lo que esté entre esas fronteras corre con el código real. Si dudas de si algo es frontera, aplica esta regla: si puede salir del proceso (red, disco fuera del repo, otro servicio, otro proceso), trátalo como frontera y mockéalo; si es lógica en memoria, déjalo real.
- **Prefiere la invocación in-process** cuando el stack la ofrece: `TestClient` (FastAPI/Starlette), `MockMvc`/`WebTestClient` (Spring), `supertest` (Express), `WebApplicationFactory` (.NET)… Levantan la aplicación real en memoria y solo le inyectas la petición sintética — es la forma más barata de maximizar código real.
- **Reusa antes de crear**: si el repo ya tiene fixtures, factories, datos semilla, `conftest.py`, `docker-compose` de dependencias locales o helpers de test, apóyate en ellos en lugar de fabricar mocks nuevos. Crear algo desde cero es el último recurso, no el primero.

### Seguridad: el código real puede tocar sistemas reales

Al importar el código del programa dentro del entorno del proyecto, el script hereda su configuración. Si una frontera se queda sin mock, el código puede leer el `.env` real y escribir en una base de datos, mandar correos o llamar a una API de producción. **Antes de ejecutar ningún script:**

1. **Revisa qué configuración carga el programa** al importarse o arrancar (`.env`, `appsettings*.json`, `application*.yml`, `config/`, variables de entorno) y localiza credenciales, cadenas de conexión y URLs de servicios reales.
2. **Neutralízala desde el propio script**: sobrescribe esas variables o propiedades con valores inocuos (base de datos en memoria o desechable, hosts inexistentes, claves ficticias) **antes** de importar o arrancar el código. Nunca modifiques los ficheros de configuración del usuario.
3. **Comprueba que cada frontera que toca el flujo está mockeada o apunta a un recurso local desechable.** Si alguna no lo está y no puedes neutralizarla, no ejecutes ese script: explica por qué y qué haría falta.
4. **Pide confirmación** con `AskUserQuestion` antes de ejecutar los scripts de cada ciclo, resumiendo qué se va a ejecutar y qué fronteras has neutralizado y cómo. Sin un "sí" explícito, no ejecutes.

### Dónde y cómo se escribe cada driver

Depende de si el stack permite importar el código desde un script suelto:

- **Stacks interpretados** — el driver vive en `docs/onboarding/04-implementacion-mocks/run-NN-<flujo>.<ext>`:
  - `.py` para Python
  - `.ts` o `.js` para Node
  - `.ps1` para programas escritos en PowerShell (módulos, scripts)
  - `.sh` para programas que son scripts de shell o CLIs que se invocan como proceso
- **Stacks compilados** (Java/Kotlin, .NET, Go…) — un script suelto no puede importar el código, así que el driver es un **test** dentro del árbol de tests del proyecto, en una carpeta o paquete `onboarding` claramente separado, donde sí puede usar `MockMvc`, `WebApplicationFactory`, `httptest`… Por ejemplo: `src/test/java/<paquete>/onboarding/Flujo01<Nombre>Test.java`, `tests/<Proyecto>.Tests/Onboarding/Flujo01<Nombre>Tests.cs`, `<paquete>/onboarding_flujo01_test.go`. En `04-implementacion-mocks/` se deja un lanzador `run-NN-<flujo>.sh` (o `.ps1` si el usuario trabaja en Windows) que ejecuta solo ese test (`mvn -Dtest=…`, `dotnet test --filter …`, `go test -run …`). El driver lee el dataset desde `04-implementacion-mocks/`. **Escribir fuera de `docs/onboarding/` requiere permiso**: pregúntalo la primera vez, indicando las rutas exactas que vas a crear. Si no hay proyecto de tests en .NET, propón crear uno mínimo que referencie el principal en lugar de forzar un script.

**Pasos:**

1. **Elige qué flujos se convierten en script.** Presenta los mini-flujos de `mini_flujos_completados` como lista **numerada en texto** (con su número estable) y pide que el usuario responda con los números o `todos`. Regístralos en `scripts_pendientes` de `_progress.md`.

2. **Inventaría lo reutilizable** antes de escribir nada: localiza los entry points/funciones documentados en los `file:line` de la Fase 2, y busca en el repo fixtures, factories y utilidades de test existentes. Anota en cada script (comentario de cabecera) qué piezas reales invoca y qué reutilizó.

3. Procesa los scripts elegidos **en ciclos de hasta 4**, igual que en la Fase 2. Por cada flujo del ciclo, genera (con el mismo número `NN` del mini-flujo):

   - **Driver ejecutable** según "Dónde y cómo se escribe cada driver"
   - **Dataset mockeado** `04-implementacion-mocks/NN-<flujo>-input.json` (o el formato natural del flujo)
   - **Salida esperada** `04-implementacion-mocks/NN-<flujo>-expected-output.md` siguiendo `templates/04-expected-output.md`

4. La cabecera de cada driver declara su contrato (formato detallado en `templates/04-expected-output.md`): qué código real invoca (`file:line` o módulo), qué fronteras mockea y por qué cada una es frontera, qué configuración neutraliza y cómo ejecutarlo. **Las dependencias son las del propio programa** (su `requirements.txt`, `package.json`, `.csproj`…): el driver se ejecuta dentro del entorno del proyecto, no declara un set de dependencias paralelo. Si necesita algo extra (raro), justifícalo en la cabecera.

5. Para las fronteras que sí haya que mockear, usa las libs estándar del stack (`unittest.mock`, `Moq`, `Mockito`, `nock`, `jest.mock`) y déjalas explícitas en el driver — cada mock con un comentario de una línea: qué frontera sustituye y qué devolvería el sistema real.

6. **Aplica la sección "Seguridad"** y pide confirmación para ejecutar los drivers del ciclo.

7. **Ejecuta cada driver de verdad** tras la confirmación, y deriva `NN-<flujo>-expected-output.md` de esa ejecución real — no lo escribas de memoria. Si el driver no corre (entorno incompleto, dependencia nativa…), dilo explícitamente en el expected-output en lugar de inventar la salida. Si la salida real difiere de la "Salida aproximada" del documento del mini-flujo, corrige ese documento y señala la diferencia en el expected-output: es justo el tipo de sorpresa que el dev debe conocer.

8. Mueve cada flujo de `scripts_pendientes` a `scripts_completados` en `_progress.md` e inserta en el marcador `<!-- placeholder:scripts -->` del checklist una entrada `- [ ] Script NN '<flujo>' ejecutado y entendido`.

9. Al cerrar cada ciclo, **párate** con `AskUserQuestion`: "Llevas X de N scripts; quedan: [lista]. ¿Seguimos con el siguiente ciclo o paramos aquí?"

10. Cuando `scripts_pendientes` esté vacía, actualiza `_progress.md` con Fase 4 completada y anuncia el final:
    > "Onboarding completo. Tienes scripts ejecutables en `04-implementacion-mocks/`. Marca el checklist a medida que los pruebes; si algo no encaja con la realidad del programa, dímelo y refinamos."

## El checklist persistente

Genera `docs/onboarding/checklist.md` en la Fase 1 a partir de `templates/checklist.md`. Es el tablero del usuario: **nunca lo reescribas entero, nunca borres entradas y nunca cambies el estado de una casilla** (`[ ]`/`[x]`) — las marcas son del usuario. Las entradas nuevas se **insertan justo encima del marcador** de su sección (`<!-- placeholder:tecnologias -->`, `<!-- placeholder:mini_flujos -->`, `<!-- placeholder:drill_down -->`, `<!-- placeholder:scripts -->`), de modo que el marcador quede siempre al final de su sección y el orden de inserción se conserve. Edita el fichero con inserciones puntuales, no regenerándolo.

## El archivo de estado `_progress.md`

Es la columna vertebral de la continuidad entre turnos. Plantilla en `templates/_progress.md`.

**Léelo al inicio de cada turno. Actualízalo al final de cada turno.** Sin esto, si el usuario cierra y vuelve mañana, perderás el contexto y le harás repetir decisiones que ya tomó.

## Estructura de salida final

```
<repo-analizado>/
├── docs/onboarding/
│   ├── _progress.md
│   ├── checklist.md
│   ├── 01-vision-general.md
│   ├── 02-flujos/
│   │   ├── 01-<flujo-1>.md
│   │   ├── 01-<flujo-1>-detalle.md   ← solo si se hizo Fase 2.2
│   │   ├── 02-<flujo-2>.md
│   │   └── ...
│   ├── 03-repaso.md
│   └── 04-implementacion-mocks/
│       ├── run-01-<flujo-1>.py       ← driver (stacks interpretados) o lanzador (compilados)
│       ├── 01-<flujo-1>-input.json
│       ├── 01-<flujo-1>-expected-output.md
│       └── ...
└── <árbol de tests>/onboarding/      ← solo en stacks compilados, con permiso del usuario
```

## Errores que evitar

- **No vuelques todas las fases en un solo turno.** Si lo haces, el diálogo se rompe.
- **No saltes la Fase 0.** Sin leer `_progress.md`, pierdes continuidad.
- **No uses `AskUserQuestion` para listas de más de 4 opciones.** Recortarías la lista; usa la lista numerada en texto.
- **No inventes mini-flujos** que no existan en el código. Si el programa solo tiene 3 flujos, lista 3.
- **No omitas el "qué NO hace"** en la visión general. Es lo que más malentendidos previene.
- **Respeta el ritmo de cada fase.** Por defecto las paradas son **entre** fases. **Excepción: las Fases 2 y 4** se procesan en ciclos de hasta 4 elementos con una parada entre ciclos — ahí sí te detienes dentro de la fase. Si el usuario dice "continúa", encadena el siguiente ciclo sin re-preguntar de más (salvo la confirmación de ejecución de la Fase 4, que es obligatoria en cada ciclo).
- **No reimplementes el programa en los scripts de la Fase 4.** El script es un driver delgado que importa e invoca el código real; si te encuentras copiando lógica del programa dentro del script, para — eso es una recreación, no una ejecución. Mockea solo las fronteras de E/S y deja todo lo demás al código verdadero.
- **No ejecutes código del programa sin neutralizar su configuración y sin confirmación.** Ver "Seguridad" en la Fase 4.
- **No presentes como verificada una salida que no has ejecutado.** En la Fase 2 es "aproximada"; solo la Fase 4 la verifica.
- **No cierres la Fase 2 con flujos seleccionados pendientes.** Un flujo solo se omite si el usuario lo ordena explícitamente (ver "Garantía de completitud" en la Fase 2). El campo `mini_flujos_pendientes` de `_progress.md` es la prueba de que no se ha olvidado ninguno.
- **Jerga huérfana = bug del documento.** Cualquier término técnico que uses en "Notas", "Variantes", el diagrama o las tablas debe estar definido en algún sitio que el lector pueda alcanzar: Fase 1 §5, un mini-flujo previo, o la tabla "Conceptos clave" del propio mini-flujo. Si un dev nuevo se encuentra una palabra que no entiende, has fallado, aunque el documento sea técnicamente correcto. **Después de escribir cada mini-flujo, relee buscando jerga huérfana** y corrígela antes de cerrarlo. Acompañar cada término técnico nuevo con una analogía cercana (un objeto cotidiano, una situación familiar) es la mejor forma de prevenirlo.

## Composición con otras skills

Cuando estén disponibles, puedes apoyarte en:

- `project-workflow-analysis-blueprint-generator` — para detectar arquitectura/stack en Fase 1 con más rigor
- `test-data-generation` — para datasets de entrada más realistas en Fase 4 (solo si el repo no tiene ya fixtures o seeds reutilizables: reusar va primero)

No son obligatorias; si están instaladas, úsalas. Si no, resuélvelo con análisis directo del repo.
