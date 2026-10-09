---
title: "Guía de estilo de documentación"
created: 2026-08-22
time: ""
creator: "Gilbert"
last update: 2026-08-22
update by: "Gilbert"
type: referencia
status: activo
fase: ""
area: gestion
editor: "Gilbert"
order: 0
tags:
  - type/referencia
---
# Guía de estilo de documentación

> [!success] Resumen
> Formato único para todos los `.md` del proyecto (guías, referencias, ADRs, READMEs y pruebas), con frontmatter listo para Obsidian. Cópialo al crear un documento nuevo, o duplica `_plantilla.md`.

## 1. Esqueleto obligatorio

Todo documento sigue esta estructura, en este orden:

```markdown
---
title: "Título del documento"      # el mismo texto del H1, entre comillas dobles
created: 2026-08-08                 # ISO 8601 (AAAA-MM-DD), fecha de creación
time: ""                            # hora de creación (HH:MM), opcional
creator: "Gilbert"                  # quién lo creó (persona)
last update: 2026-08-08             # ISO 8601, fecha del último cambio de contenido
update by: "Gilbert"                # quién hizo el último cambio
type: manual                        # manual | referencia | adr | readme | prueba | doc
status: activo                      # borrador | activo | aceptada | obsoleto
fase: 3                             # 0–5 (fase del roadmap); "" si es transversal
area: firmware                      # electronica | firmware | 3d | audio | gestion; "" si es transversal
editor: "Gilbert"            # responsable del documento
order: 0                            # entero para ordenar dentro de la carpeta
tags:
  - type/manual                     # debe coincidir con el campo `type`
---
# Título del documento

> [!success] Resumen
> una línea diciendo qué resuelve y para quién.

## 1. Primera sección
...

---
## 2. Segunda sección
...

---
## Enlaces
[[proyectos|Proyectos Electrónica]]
```

---
## 2. Reglas

- **Frontmatter completo**: usar los trece campos del esquema (`title`, `created`,
  `time`, `creator`, `last update`, `update by`, `type`, `status`, `fase`, `area`,
  `editor`, `order`, `tags`). Los que no apliquen se dejan vacíos (`time: ""`,
  `fase: ""`), no se omiten.
- **Comillas**: los valores de texto libre (`title`, `creator`, `update by`, `editor`)
  van entre **comillas dobles**. Las fechas ISO, `order` (entero) y los valores de
  lista cerrada (`type`, `status`, `fase`, `area`) van sin comillas.
- **`type` y `tags` deben concordar** (`type: manual` ⇒ `tags: [type/manual]`).
- **Fechas siempre en ISO 8601** (`2026-08-08`). Nunca `DD/MM/AAAA`.
- **La fecha vive solo en el frontmatter** (`created` / `last update`). No repetir un
  footer `_Last updated_` en el cuerpo.
- **El estado vive solo en el frontmatter** (`status`). No repetir un `**Estado:**` en
  el cuerpo (aplica sobre todo a los ADR).
- **`title` = texto del H1**, entre comillas dobles.
- **`order`**: entero para ordenar los documentos de una misma carpeta en Obsidian.
  Si el archivo tiene prefijo numérico (`01_flash.md`), usar ese número; si no, `0`.
- **`fase` y `area`** clasifican el documento para filtrarlo en Obsidian. Un documento
  transversal (README, roadmap, este estilo) deja `fase: ""` y usa `area: gestion`.
- **Sin bloque de autor en el cuerpo**: la autoría vive solo en el frontmatter
  (`creator` / `editor` / `update by`). No repetir un bloque `**Autor:**` en el cuerpo.
- **Resumen como callout**: usar `> [!success] Resumen` seguido del texto en líneas `>`.
- **Un solo idioma: español.**
- **Títulos**: un único `# H1` (el título del documento). Las secciones de contenido son
  `## 1.`, `## 2.`; las subsecciones `### 1.1`, `### 1.2`. No usar `#` para secciones.
  Única excepción sin numerar: el apéndice `## Enlaces` (ver abajo).
- **Separadores de sección**: una regla horizontal `---` entre secciones consecutivas
  (antes de cada `## ` salvo la primera). Da respiro visual en Obsidian.
- **Sección `Enlaces`** (apéndice): el documento cierra con `## Enlaces` seguido de
  wikilinks `[[ ]]` a la nota hub del proyecto en `Enlaces/` y a documentos relacionados. Es la única
  sección sin numerar; los enlaces internos del cuerpo siguen usando rutas relativas.
- **Código y rutas** siempre en `backticks`; bloques con el lenguaje declarado
  (```bash, ```cpp, ```yaml).

---
## 3. Valores permitidos de `type`

| `type`        | Uso                                                        |
| ------------- | ---------------------------------------------------------- |
| `manual`      | Procedimiento paso a paso (infraestructura, despliegue).   |
| `referencia`  | Explica cómo funciona algo, sin ser un procedimiento.      |
| `adr`         | Registro de una decisión de arquitectura (ver sección 5).  |
| `readme`      | README de una carpeta/proyecto.                            |
| `prueba`      | Registro de una prueba o experimento.                      |
| `doc`         | Documento de proyecto que no encaja en las categorías anteriores. |

---
## 4. Valores de `status`, `fase` y `area`

**`status`** — estado de vida del documento:

| `status`     | Significado                                               |
| ------------ | -------------------------------------------------------- |
| `borrador`   | En redacción; aún no fiable.                             |
| `activo`     | Vigente y en uso.                                        |
| `aceptada`   | Decisión aprobada (para ADRs).                          |
| `obsoleto`   | Superado o histórico; se conserva como registro.        |

**`fase`** — fase del roadmap a la que pertenece el documento: `0`, `1`, `2`, `3`, `4`
o `5`. Si es transversal a todas, `fase: ""`.

**`area`** — disciplina principal del documento, según la leyenda del roadmap:

| `area`        | Alcance                                        |
| ------------- | ---------------------------------------------- |
| `electronica` | Esquemático, PCB, alimentación. 🔌             |
| `firmware`    | Código, decodificación, drivers, UI. 💾        |
| `3d`          | Carcasa, modelado, impresión. 🧊               |
| `audio`       | DAC, etapa analógica, ruido, calidad. 🎧       |
| `gestion`     | Roadmap, estilo, README, procesos. 📋          |

---
## 5. Plantilla de ADR

Un ADR (*Architecture Decision Record*) documenta **por qué** se tomó una decisión, no
solo cuál. Usa `type: adr`, `status` refleja el estado de la decisión (`aceptada`,
`obsoleto`…) y **no** se repite el estado en el cuerpo. Estructura recomendada por
decisión:

```markdown
## N. Título de la decisión

**Contexto.** Qué problema o restricción motiva la decisión.

**Opciones consideradas.**
- *Opción A.* Pros/contras.
- *Opción B.* Pros/contras.

**Decisión.** Qué se eligió.

**Consecuencias.**
- ✅ Efecto positivo.
- ⚠️ Coste o límite aceptado.
```

---
## 6. Reutilizar la plantilla en otros proyectos

Este formato es genérico; para adaptarlo a otro proyecto similar (hardware + firmware),
ajusta solo estas piezas:

- **`fase`**: cambia el rango (`0–5` aquí) al número de fases del roadmap del nuevo
  proyecto. Si el proyecto no se organiza por fases, deja `fase: ""` siempre.
- **`area`**: redefine el conjunto según las disciplinas del nuevo proyecto (p. ej.
  quita `audio` si no aplica, añade `mecanica`, `web`, etc.). Mantén el mismo `area:
  gestion` para documentos transversales.
- **Sección `Enlaces`**: apunta a la MOC de *ese* vault/proyecto, no a `[[Proyectos
  Electrónica]]`.
- Todo lo demás (esquema de frontmatter, reglas, tipos, ADR) se reutiliza tal cual.
  Duplica `_plantilla.md` para arrancar cada documento nuevo.

---
## Enlaces
[[proyectos_personal|Proyectos Electrónica]]
