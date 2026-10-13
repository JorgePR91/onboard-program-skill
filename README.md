# onboard-program

<p align="center">
  <img src="IMAGE.svg" width="350" alt="onboard-program" />
</p>

<p align="center"><em>En el curro te han dado un programa y tienes que entenderlo PARA AYER...</em></p>

**onboard-program** es una skill para Claude Code que acompaña a un desarrollador/a nuevo/a, paso a paso, en la comprensión de un programa, servicio o repositorio que no conoce. Convierte el "no entiendo este programa" en un recorrido guiado que va de lo general a lo concreto y deja en el repo documentación, diagramas, un checklist y scripts ejecutables con datos mockeados.

- [Qué obtienes](#qué-obtienes)
- [Instalación](#instalación)
- [Uso](#uso)
- [Cómo funciona](#cómo-funciona)
- [Principios de diseño](#principios-de-diseño)
- [Estructura del repositorio](#estructura-del-repositorio)

## Qué obtienes

Al terminar el recorrido, el repositorio analizado contiene una carpeta `docs/onboarding/` como esta:

```
<repo-analizado>/
└── docs/onboarding/
    ├── _progress.md                  ← estado del onboarding (para retomarlo)
    ├── checklist.md                  ← tu tablero de progreso
    ├── 01-vision-general.md          ← qué hace, qué NO hace, cómo lo hace
    ├── 02-flujos/
    │   ├── 01-<flujo-1>.md
    │   ├── 01-<flujo-1>-detalle.md   ← solo si pediste drill-down
    │   ├── 02-<flujo-2>.md
    │   └── ...
    ├── 03-repaso.md                  ← dependencias, patrones y riesgos
    └── 04-implementacion-mocks/
        ├── run-01-<flujo-1>.py       ← ejecuta el código real con datos sintéticos
        ├── 01-<flujo-1>-input.json
        ├── 01-<flujo-1>-expected-output.md
        └── ...
```

En stacks compilados (Java, .NET, Go…), el driver de cada flujo es un test dentro del árbol de tests del proyecto, en una carpeta `onboarding/`, y `run-NN-<flujo>` solo lo lanza. Claude te pide permiso antes de escribir fuera de `docs/onboarding/`. Al empezar te pregunta también si quieres versionar `docs/onboarding/` o añadirla a `.gitignore`.

Cada documento incluye diagramas Mermaid, referencias `fichero:línea` al código y, cuando hace falta, un glosario con analogías cotidianas para que no quede jerga sin explicar.

## Instalación

No hay que compilar ni instalar dependencias: basta con copiar la carpeta `onboard-program` (con `SKILL.md` y `templates/`) al directorio de skills de usuario.

**Windows (PowerShell)**, desde la raíz de este repo:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse .\onboard-program "$HOME\.claude\skills\"
```

**macOS / Linux**:

```bash
mkdir -p ~/.claude/skills
cp -r onboard-program ~/.claude/skills/
```

El resultado debe quedar así:

```
~/.claude/skills/onboard-program/
├── SKILL.md
├── templates/
└── evals/        ← casos de prueba (opcional, no afecta al uso)
```

Después, abre (o reinicia) Claude Code para que detecte la skill. Al ser una skill de usuario, queda disponible en todos tus proyectos.

> **¿Solo para un proyecto?** Cópiala en `.claude/skills/onboard-program/` dentro de ese repositorio y estará disponible únicamente allí (y para quien clone el repo).

## Uso

Abre Claude Code en el repositorio que quieres entender y escribe algo como:

- *"Quiero entender este programa"*
- *"Onboarding de este repo"*
- *"Explícame este código paso a paso"*
- *"Preséntame este servicio"*
- *"Ayúdame a comprender qué hace este código"*

No hace falta usar la palabra "onboarding": la skill se activa siempre que pidas comprender o explorar un programa que no conoces. Si no queda claro qué programa analizar, Claude te pedirá la ruta.

**Retomar otro día.** El estado se guarda en `docs/onboarding/_progress.md` dentro del repo analizado. Si cierras la sesión y vuelves más tarde, Claude lo lee y continúa donde lo dejaste, sin repetir fases ni volver a preguntarte lo que ya decidiste. También guarda el commit analizado: si el código ha cambiado desde entonces, te avisa de que las referencias `fichero:línea` pueden haberse desplazado.

**Elegir de una lista larga.** Cuando hay más de cuatro opciones (tecnologías, mini-flujos, scripts), Claude te muestra una lista numerada y respondes con los números: `1, 3, 7`, `2-5`, `todos` o `ninguno`.

## Cómo funciona

El recorrido tiene cuatro fases. Al final de cada una, Claude **se para y te pregunta** cómo seguir.

| Fase | Objetivo | Salida |
|------|----------|--------|
| 0. Recepción | Comprobar si hay un onboarding a medias y retomarlo | Lectura de `_progress.md` |
| 1. Visión general | Qué hace, qué no hace y cómo está construido | `01-vision-general.md` + diagrama Mermaid |
| 2. Mini-flujos | Recorrer las funcionalidades una a una, las que tú elijas | `02-flujos/NN-<nombre>.md` por flujo |
| 2.2 Drill-down *(opcional)* | Funciones clave, ficheros implicados y aspectos transversales | `02-flujos/NN-<nombre>-detalle.md` |
| 3. Repaso | Consolidar: dependencias, patrones repetidos y riesgos | `03-repaso.md` |
| 4. Implementación con mocks | Ejecutar los flujos sobre el código real con datos sintéticos | `04-implementacion-mocks/` |

Algunos detalles de cada fase:

- **Fase 1 se adapta a tu nivel.** Claude detecta las tecnologías clave del programa (lenguaje, framework, base de datos, broker…) y te pregunta cuáles no dominas. Solo explica esas, así no aburre a un perfil senior ni deja perdido a uno junior.
- **Fase 2 trabaja por ciclos.** Claude identifica *todos* los mini-flujos (endpoints, comandos CLI, jobs, eventos consumidos…), tú eliges cuáles explorar y se documentan en tandas de hasta 4, con una parada entre tandas. Ningún flujo elegido se descarta sin que lo confirmes.
- **Fase 2 da una salida aproximada.** Cada mini-flujo incluye la salida que se deduce leyendo el código, marcada como aproximada. La Fase 4 la verifica ejecutando y, si no coincide, corrige el documento.
- **Fase 4 también va por ciclos, y pide confirmación antes de ejecutar.** Eliges qué flujos convertir en script. Antes de ejecutar cada tanda, Claude neutraliza la configuración que podría tocar sistemas reales (credenciales, cadenas de conexión, URLs de producción) y te pide permiso. Tus ficheros de configuración no se modifican.
- **Numeración estable.** Cada mini-flujo recibe un número (`01`, `02`…) que no cambia aunque lo explores en otro orden o descartes otros. Ese número aparece en todos los sitios donde se menciona el flujo: la lista inicial, `_progress.md`, nombres de fichero, títulos, checklist, repaso y scripts de la Fase 4 (`run-NN-<flujo>.<ext>`, `NN-<flujo>-input.json`, `NN-<flujo>-expected-output.md`). El flujo `07` es siempre el `07`.

## Principios de diseño

**Dialogar, no volcar.** La skill nunca resuelve el onboarding en un único mensaje gigante. Nadie asimila cuatro fases de golpe, y solo preguntando entre fases se puede saber qué flujos te interesan o dónde quieres profundizar. El checklist tiene sentido porque lo vas marcando a medida que avanzas.

**Mock mínimo, código real máximo.** Los scripts de la Fase 4 **no reimplementan** los flujos: son drivers delgados que importan e invocan el código del propio programa, el mismo que leíste en la Fase 2. En concreto:

- Solo se mockean las fronteras de E/S (red, base de datos, colas, reloj, aleatoriedad…).
- Se prefiere la invocación in-process cuando el stack lo permite (`TestClient`, `MockMvc`, `supertest`, `WebApplicationFactory`…).
- Se reutilizan los fixtures y helpers de test que el repo ya tenga.
- Las dependencias son las del propio programa; los scripts no declaran un entorno paralelo.
- La salida esperada se obtiene de una ejecución real del script, nunca se escribe de memoria. Si el script no puede ejecutarse, se dice explícitamente.

**Sin jerga huérfana.** Todo término técnico que aparezca en la documentación está definido en algún sitio al que el lector pueda llegar, y los conceptos nuevos van acompañados de una analogía cotidiana.

## Estructura del repositorio

| Ruta | Función |
|------|---------|
| `README.md` | Esta documentación, para personas (no forma parte de la skill) |
| `IMAGE.svg` | Imagen del README |
| `onboard-program/SKILL.md` | Instrucciones para Claude: qué hacer en cada fase |
| `onboard-program/templates/` | Plantillas que Claude rellena al generar cada documento |
| `onboard-program/evals/evals.json` | Casos de prueba para comprobar que la skill se activa y se comporta como debe |

Plantillas disponibles: `01-vision-general.md`, `02-flujo.md`, `02-flujo-detalle.md`, `03-repaso.md`, `04-expected-output.md`, `checklist.md` y `_progress.md`.

### Skills complementarias

Si las tienes instaladas, la skill puede apoyarse en ellas. No son obligatorias:

- `project-workflow-analysis-blueprint-generator`: para detectar arquitectura y stack con más rigor en la Fase 1.
- `test-data-generation`: para generar datos mockeados más realistas en la Fase 4.
