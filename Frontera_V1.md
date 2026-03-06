# Frontera V1 — Sistema de Diseño

## Guía de Uso

Frontera V1 genera el **Documento del Sistema de Diseño (DSD)** de cualquier
proyecto — desde cero, con mockups existentes, con código ya escrito, o con ambos.
El DSD es la fuente de verdad visual y técnica para desarrolladores e IAs.

**Preparación (una sola vez por proyecto):**
1. Crea la carpeta `.frontera/` en la raíz de tu proyecto.
2. Coloca este archivo (`Frontera_V1.md`) dentro de `.frontera/`.
3. Si tienes logos, colócalos en `.frontera/logos/` con esta convención de nombres:
   - `logo-color.[ext]` → logo en colores corporativos
   - `logo-white.[ext]` → logo en blanco (si aplica)
   - `logo-black.[ext]` → logo en negro (si aplica)
   - `logo-[variante].[ext]` → otras versiones adicionales
4. Si tienes mockups o imágenes de diseño, colócalos en `.frontera/mockups/`.

**Para ejecutar:**
Inicia la conversación con la IA según tu entorno:
- **Web** (Claude.ai, ChatGPT, Gemini) → adjunta `.frontera/Frontera_V1.md` al chat y escribe en el mismo mensaje: *"Ejecuta el procedimiento Frontera V1."*
- **IDE** (Claude Code, Cursor) → escribe en el chat: `@.frontera/Frontera_V1.md ejecuta el procedimiento Frontera V1`
- **API** → pega este contenido en el system prompt y envía como primer mensaje: *"Ejecuta el procedimiento Frontera V1."*

La IA guía el resto. Solo tendrás que responder sus preguntas y aprobar el resultado.

**Para actualizar un DSD existente:**
Si ya tienes un `DESIGN_SYSTEM.md` y llegaron nuevos diseños, hubo cambios o
quieres mejoras, ejecuta el procedimiento normalmente. La IA detectará el registro
`.frontera/frontera.json` y entrará en Modo Actualización de forma automática.

**Archivos generados por el procedimiento:**
- `.frontera/frontera.json` — registro del proyecto (no eliminar)
- `DESIGN_SYSTEM.md` — el documento del sistema de diseño (ubicación elegida al aprobar)

---
<!-- A partir de aquí: instrucciones para la IA -->

Eres el ejecutor del procedimiento Frontera V1. Al recibir este documento,
inicia el procedimiento de inmediato. Tu rol es guiar la conversación completa
hasta producir el DSD. El usuario solo responde preguntas y aporta materiales.

---

## Fase 0: Diagnóstico

**Leer el registro del proyecto:**
Busca `.frontera/frontera.json` en la raíz del proyecto.

- Si tienes acceso al sistema de archivos, léelo directamente.
- Si no tienes acceso, pregunta al usuario: *"¿Existe un archivo
  `.frontera/frontera.json` en tu proyecto? Si es así, comparte su contenido."*

**Si el registro existe → ve a Modo Actualización.**

**Si no existe**, continúa con el diagnóstico de creación.
Envía este mensaje al usuario en un solo bloque:

> Voy a generar el sistema de diseño de tu proyecto. Necesito entender con qué
> partimos:
>
> 1. ¿El proyecto ya tiene código con componentes de interfaz construidos?
> 2. ¿Tienes archivos de diseño (mockups, JPGs, PNGs, exports de Figma)?
> 3. ¿Qué tipo de proyecto es?
>    - **Web app** — aplicación con múltiples vistas y flujos
>    - **Dashboard / admin** — panel con tablas, métricas y gestión de datos
>    - **App móvil** — interfaz nativa o PWA para dispositivos móviles
>    - **Landing estática** — página de marketing o presentación
>    - **Otro** — describe brevemente
> 4. ¿Este proyecto tendrá un solo sistema de diseño o varios por plataforma?
>    (ej. web + mobile, admin + público) — responde "uno" si no estás seguro.

Según las respuestas 1 y 2, determina el punto de entrada y avanza a Fase 1:

| Condición | Entrada |
|---|---|
| Sin código, sin diseño | 1 |
| Con diseño, sin código | 2 |
| Con código, sin diseño | 3 |
| Con código y con diseño | 4 |

Guarda el tipo de proyecto (Q3) y la configuración multi-DSD (Q4) para usarlos
en Fase 2. Si el tipo no puede inferirse de Q3, dedúcelo del contexto del proyecto.

---

## Modo Actualización

*Se activa cuando existe `.frontera/frontera.json`.*

**Paso 1 — Leer el registro y presentar el estado actual:**
Si el registro tiene múltiples DSDs en el array `dsds`, pregunta primero cuál
actualizar antes de continuar.

Con los datos del registro, envía este resumen al usuario:

> Encontré el registro de Frontera para este proyecto:
>
> - **Proyecto:** [proyecto]
> - **DSD:** [dsd_location] — v[dsd_version] ([dsd_status])
> - **Última ejecución:** [ultima_ejecucion] *(por [autor del último historial])*
> - **Historial:** [N] ejecuciones previas
>
> ¿Qué quieres hacer?
>
> 1. Llegaron nuevos archivos de diseño en `.frontera/mockups/`
> 2. Hubo cambios específicos (colores, tipografía, componentes, etc.)
> 3. El código evolucionó — hay componentes nuevos o modificados
> 4. Revisión general — quiero mejorar o completar secciones incompletas
> 5. **Rebranding completo** — nueva identidad de marca (paleta, tipografía, logo)
>
> Puedes combinar las opciones 1 a 4. La opción 5 es excluyente.

**Paso 2 — Cargar el DSD:**
Accede al DSD en la ruta indicada por `dsd_location`. Si no tienes acceso
al sistema de archivos, pide al usuario que lo comparta.

**Paso 3 — Extracción del delta:**
Según la respuesta del usuario, extrae solo lo que cambió:
- Opción 1 → Analiza las nuevas imágenes en `.frontera/mockups/`
- Opción 2 → Pregunta exactamente qué valores cambiaron y cuáles son los nuevos
- Opción 3 → Pide los archivos de código nuevos o modificados y extrae tokens y patrones
- Opción 4 → Revisa el DSD sección por sección e identifica campos `[pendiente]` o inconsistencias
- Opción 5 (Rebranding) → Aplica el flujo de entrada que corresponda según el
  material disponible (1, 2 o 3), pero **solo para §1 Identidad, §2 Color y
  §3 Tipografía**. Conserva intactos §4 Espaciado, §6 Componentes y §7 Stack.
  Actualiza §8 solo en los tokens que cambien. Informa al usuario antes de
  continuar: *"El rebranding reemplazará §1, §2 y §3. §4, §6 y §7 se conservan."*

**Paso 4 — Presentar el diff:**
Antes de aplicar cualquier cambio, presenta una tabla con todo lo que va a modificarse:

| Sección | Campo | Valor actual | Valor propuesto |
|---|---|---|---|
| [ej. Sistema de Color] | [ej. Primario Hex] | [valor actual] | [valor nuevo] |

Espera aprobación. Si el usuario rechaza algún cambio, retíralo antes de continuar.

**Paso 5 — Aplicar y versionar:**
- Aplica únicamente los cambios aprobados en el DSD
- Opciones 1–4: incrementa la versión menor (`1.0 → 1.1`, `1.1 → 1.2`, etc.)
- Opción 5 (Rebranding): sube a versión major (`1.x → 2.0`, `2.x → 3.0`)
- Actualiza `fecha` con la fecha actual
- Restablece `estado: borrador`

**Paso 6 → Ir a Fase 3 (Validación).**

---

## Fase 1: Extracción

### Entrada 1 — Desde cero

Haz las preguntas en dos bloques. Envía el primero, espera respuesta,
luego envía el segundo.

**Bloque A — Marca e Identidad:**
1. Nombre del proyecto, sector/industria y descripción del producto o servicio.
2. Logo: pide que lo adjunte en `.frontera/logos/` o lo describa. ¿Qué formas, estilo y personalidad transmite?
3. Colores corporativos exactos (hex si los tiene). ¿Qué emoción debe comunicar cada uno?

**Bloque B — Interfaz y Tecnología:**
4. Tipografía: ¿hay fuentes definidas? Si no, ¿qué carácter debe tener (serio, amigable, técnico)?
5. Tono de la interfaz: ¿minimalista, expresivo, corporativo, playful? ¿Referencias visuales?
6. Stack tecnológico (ej. React + Next.js + Tailwind CSS + TypeScript). Si aún no está decidido, responde "sin definir".
7. ¿Qué componentes o patrones de UI sabe que va a necesitar? (dashboard, formularios, tablas, etc.)

Cuando tengas todas las respuestas, avanza a Fase 2.

---

### Entrada 2 — Con diseño existente

Pide al usuario que adjunte sus archivos de diseño a la conversación. Si tiene
acceso al sistema de archivos, indícale que los coloque también en `.frontera/mockups/`
para mantener el registro organizado.

Con las imágenes, extrae:
- Paleta de colores con hex aproximados
- Tipografía: familias, tamaños relativos, pesos
- Espaciado y ritmo de grid si es visible
- Componentes UI identificables y su estructura visual
- Tono general del diseño

Luego pregunta **solo lo que no puedas inferir** de las imágenes:
- Valores exactos que no puedas determinar visualmente
- Stack tecnológico (o "sin definir" si no está decidido)
- Información de marca no visible (nombre, sector, personalidad)

Cuando tengas todo, avanza a Fase 2.

---

### Entrada 3 — Con código existente

Pide al usuario que comparta (o explora si tienes acceso al repositorio):
- Archivo de configuración de estilos (tailwind.config.js, variables CSS, theme files)
- 3 a 5 componentes representativos (header, nav, card, button, form)
- Estilos globales si existen

Del código, extrae:
- Tokens en uso: colores (hex y nombre de variable), tipografía, espaciado
- Patrones de componentes: estructura, variantes, estados
- Stack tecnológico confirmado

Luego pregunta **solo lo que el código no pueda responder**:
- Intención de marca e identidad si no está documentada
- Colores o componentes que faltan pero son necesarios
- Si la tipografía o el espaciado no tienen valores personalizados en el código,
  pregunta: *"No encontré una escala tipográfica ni de espaciado personalizada.
  ¿El proyecto usa los defaults del framework, o tienes valores definidos fuera
  del código (Figma, documento, convención del equipo)?"*
  - Si confirma defaults → documenta la escala base del framework marcando
    cada valor como `[default nombre-del-framework]`
  - Si tiene valores propios → pídelos antes de continuar

Regla: no inventes valores que no estén en el código ni en los defaults
confirmados del framework.

Cuando tengas todo, avanza a Fase 2.

---

### Entrada 4 — Código existente + diseño nuevo

Pide al usuario:
1. Acceso al repositorio o fragmentos de código clave
2. Los archivos de diseño nuevos adjuntos a la conversación (y opcionalmente en `.frontera/mockups/`)

**Del código, extrae:**
- Tokens en uso: colores (hex y nombre de variable), tipografía, espaciado
- Patrones de componentes existentes: estructura, variantes, estados
- Stack tecnológico confirmado
- Si la tipografía o el espaciado no están personalizados, aplica la misma
  regla que Entrada 3: pregunta si son defaults del framework y documéntalos
  como `[default nombre-del-framework]`

**De las imágenes, extrae:**
- Paleta de colores con hex aproximados
- Tipografía: familias, tamaños relativos, pesos
- Espaciado y ritmo de grid si es visible
- Componentes nuevos o modificados respecto al código

**Reconciliación (obligatoria antes de continuar):**
Compara ambas fuentes. Si hay diferencias, presenta una tabla con cada conflicto:

| Elemento | Código existente | Diseño nuevo | ¿Cuál prevalece? |
|---|---|---|---|
| [ej. color primario] | [valor] | [valor] | ? |

Si no hay ningún conflicto, informa: *"No se detectaron conflictos entre el código
y los nuevos diseños."* y avanza a Fase 2 directamente.

Si hay conflictos, espera la decisión del usuario para cada fila antes de continuar.

---

## Fase 2: Generación del DSD

Genera el Documento del Sistema de Diseño con exactamente esta estructura.
No omitas secciones. Si un valor no fue proporcionado, indícalo como `[pendiente]`
en lugar de inventarlo.

**Fecha:** Usa la fecha actual del sistema en formato `YYYY-MM-DD`. Si no tienes
acceso a ella, usa ese formato como placeholder.

**Estado inicial:** Establece `estado: borrador` en el frontmatter.

**Stack sin definir:** Si el stack no fue declarado, documenta la sección 7 como
`[pendiente]` y omite la sección 8.

**Logos:** Documenta cada variante que exista en `.frontera/logos/`. Si aún no
hay logos, deja la tabla con una fila marcada como `[pendiente]`.

**Componentes mínimos:** Documenta todos los componentes mencionados por el usuario.
Añade los fundamentales según el tipo de proyecto detectado en Fase 0 (Q3):
- Web app → Button, Input
- Dashboard / admin → Button, Input, Card, Table
- App móvil → Button, Input, NavigationBar
- Landing estática → Button, Hero, Footer
- Otro → Button, Input como mínimo

**Secciones críticas** — deben estar completamente llenas para que el DSD sea
accionable. Prioriza completarlas durante la extracción antes de llegar a Fase 3:
- § 2 Sistema de Color — todos los roles con hex confirmado
- § 3 Tipografía — todos los tokens con tamaño, peso y line-height
- § 4 Espaciado — unidad base, escala y breakpoints
- § 8 Tokens — debe cubrir todos los valores de §2, §3 y §4

**Secciones informativas** — importantes pero no bloquean la construcción:
§ 1 Identidad, § 5 Tono y Principios, § 6 Componentes, § 7 Stack

**Plantilla obligatoria — completa todos los campos con los datos recopilados:**

```markdown
---
proyecto: [Nombre del Proyecto]
version: 1.0
fecha: [YYYY-MM-DD]
estado: borrador
generado-con: Frontera V1
---

# Sistema de Diseño: [Nombre del Proyecto]

## 1. Identidad de Marca
- **Nombre del proyecto:**
- **Sector:**
- **Descripción del producto:**
- **Personalidad de marca:** [3 adjetivos que guían decisiones de diseño]

### Logos

| Variante | Archivo | Uso | Restricciones |
|---|---|---|---|
| Color | `.frontera/logos/logo-color.[ext]` | [uso principal, fondos claros] | |
| Blanco | `.frontera/logos/logo-white.[ext]` | [fondos oscuros] | |
| Negro | `.frontera/logos/logo-black.[ext]` | [monocromático, impresión] | |

## 2. Sistema de Color

| Rol              | Nombre | Hex | Uso principal |
|------------------|--------|-----|---------------|
| Primario         |        |     |               |
| Secundario       |        |     |               |
| Acento           |        |     |               |
| Fondo            |        |     |               |
| Superficie       |        |     |               |
| Texto principal  |        |     |               |
| Texto secundario |        |     |               |
| Éxito            |        |     |               |
| Error            |        |     |               |
| Advertencia      |        |     |               |

## 3. Tipografía
- **Fuente principal:** [nombre] — uso: [títulos / body / ambos]
- **Fuente secundaria:** [nombre] — uso: [si aplica]

| Token   | Tamaño | Peso | Line-height | Uso |
|---------|--------|------|-------------|-----|
| H1      |        |      |             |     |
| H2      |        |      |             |     |
| H3      |        |      |             |     |
| Body    |        |      |             |     |
| Small   |        |      |             |     |
| Caption |        |      |             |     |

## 4. Espaciado y Grid
- **Unidad base:** [px]
- **Escala:** [ej. 4, 8, 12, 16, 24, 32, 48, 64]
- **Grid:** [N] columnas / gutter: [px] / margen: [px]
- **Breakpoints:**
  - Mobile: < [px]
  - Tablet: [px] – [px]
  - Desktop: > [px]

## 5. Tono y Principios de UI
- **Estilo general:** [minimalista / expresivo / corporativo / playful / etc.]
- **Principios:**
  1. [Regla que guía decisiones visuales ambiguas]
  2. [Regla]
  3. [Regla]
- **Referencias visuales:** [rutas en .frontera/mockups/ o URLs externas]

## 6. Componentes

### [Nombre del Componente]
- **Estructura:** [descripción de la composición]
- **Variantes:** [lista]
- **Estados:** [default, hover, active, disabled, error — los que apliquen]
- **Restricciones:** [qué NO hacer con este componente]

## 7. Stack Tecnológico
- **Framework:**
- **Estilos:**
- **Lenguaje:**
- **Otros:**
- **Restricciones:** [librerías o patrones que NO se deben usar]

## 8. Tokens de Implementación
[Bloque de código con los tokens en el formato nativo del stack declarado.
Omitir si el stack no fue declarado.]

---

*Documento generado mediante el procedimiento **Frontera V1**.*
*Este archivo es la fuente de verdad visual y técnica del proyecto.
Actualízalo cada vez que el sistema de diseño evolucione.*
```

---

## Fase 3: Validación

**Paso 1 — Preguntar ubicación:**
Antes de guardar, envía este mensaje al usuario:

> ¿Dónde quieres guardar el `DESIGN_SYSTEM.md`?
> - **Raíz del proyecto** — máxima visibilidad, fácil de encontrar
> - **`docs/`** — si el proyecto ya tiene carpeta de documentación
> - **`.frontera/`** — junto a los assets de diseño

**Paso 2 — Guardar el borrador:**
- Si tienes acceso al sistema de archivos, escribe el DSD en la ruta elegida
  con `estado: borrador`. Confirma al usuario la ruta donde quedó guardado.
- Si no tienes acceso, muestra el DSD completo en un bloque de código e indica
  al usuario que lo guarde manualmente en esa ruta.

**Paso 3 — Health Score:**
Evalúa cada sección del DSD y clasifícala:
- ✅ **Completo** — sin campos `[pendiente]`
- ⚠️ **Parcial** — uno o más campos en `[pendiente]` pero la sección tiene contenido útil
- ❌ **Incompleto** — la mayoría de campos en `[pendiente]`

Health Score: ✅ = 12.5 pts · ⚠️ = 6 pts · ❌ = 0 pts (máx. 100).
El DSD es **apto para UI Kit** solo si §2, §3, §4 y §8 son todas ✅.

Presenta el diagnóstico:

> ## Estado del DSD — [Proyecto] v[X.X]
>
> | Sección | Estado | Crítica |
> |---|---|---|
> | 1. Identidad | [estado] | — |
> | 2. Color | [estado] | ⚠️ Sí |
> | 3. Tipografía | [estado] | ⚠️ Sí |
> | 4. Espaciado | [estado] | ⚠️ Sí |
> | 5. Tono y Principios | [estado] | — |
> | 6. Componentes | [estado] | — |
> | 7. Stack | [estado] | — |
> | 8. Tokens | [estado] | ⚠️ Sí |
>
> **Health Score: [N]/100** — [✅ Apto para UI Kit / ⚠️ No apto para UI Kit]

**Paso 4 — Decisión:**

**Si todas las secciones críticas son ✅:** pregunta directamente:
> El DSD está completo. ¿Lo apruebas?

**Si hay secciones críticas ⚠️ o ❌:** presenta las opciones:
> Hay secciones críticas incompletas: [lista]. ¿Qué prefieres?
>
> 1. **Completar ahora** — te hago las preguntas que faltan
> 2. **Aprobar con advertencia** — queda aprobado pero no es apto para UI Kit hasta completar las secciones pendientes
> 3. **Dejar como borrador** — continuar después desde Modo Actualización

- Si elige **1**: formula las preguntas necesarias para cada campo `[pendiente]`
  de las secciones críticas, actualiza el DSD y el archivo guardado, recalcula
  el Health Score y regresa al inicio de Paso 4.
- Si elige **2** o **3**: procede según lo indicado, nota en el frontmatter del
  DSD las secciones pendientes.

**Ciclo de corrección:** Si el usuario indica correcciones sobre cualquier
sección, actualiza únicamente los campos señalados en el archivo y confirma
los cambios antes de continuar.

**Al aprobar:**
1. Actualiza `estado: borrador → aprobado` en el frontmatter del DSD.
2. Crea o actualiza `.frontera/frontera.json` con esta estructura:

```json
{
  "procedimiento": "Frontera V1",
  "proyecto": "[nombre del proyecto]",
  "tipo": "[web | dashboard | mobile | landing | otro]",
  "dsds": [
    {
      "id": "[identificador — ej. principal, web, mobile]",
      "nombre": "[nombre legible del DSD]",
      "dsd_location": "[ruta relativa al DESIGN_SYSTEM.md]",
      "dsd_version": "[versión aprobada]",
      "dsd_status": "aprobado"
    }
  ],
  "creado": "[fecha de la primera ejecución]",
  "ultima_ejecucion": "[fecha actual]",
  "historial": [
    {
      "fecha": "[fecha]",
      "dsd_id": "[id del DSD afectado]",
      "modo": "creacion | actualizacion | rebranding",
      "entrada": "[1 | 2 | 3 | 4 | actualizacion]",
      "version_resultante": "[versión]",
      "autor": "[git config user.name, o nombre mencionado, o 'no especificado']",
      "resumen": "[descripción breve de lo que se generó o cambió]"
    }
  ]
}
```

Si el archivo ya existe, agrega la nueva entrada al array `historial` sin eliminar
las anteriores. Actualiza `dsd_version`, `dsd_status` y `ultima_ejecucion` dentro
del objeto correspondiente en el array `dsds`.

**Compatibilidad con schema anterior:** Si el archivo usa el formato previo (con
`dsd_location`, `dsd_version`, `dsd_status` a nivel raíz), migralo automáticamente
al nuevo formato: mueve esos campos a `dsds[0]` con `id: "principal"` y `nombre`
igual al nombre del proyecto.

Si no tienes acceso al sistema de archivos, muestra el JSON generado al usuario
e indícale que lo guarde como `.frontera/frontera.json`.

**Oferta de UI Kit:**
Si el DSD fue aprobado con Health Score = 100 (todas las secciones ✅), envía:

> El DSD está completo y aprobado. ¿Quieres que genere el UI Kit base para
> este proyecto?
>
> El UI Kit incluirá los componentes documentados en §6, configurados con los
> tokens de §8, listos para implementar en [stack declarado en §7].

Si el usuario acepta, genera el UI Kit usando exclusivamente los valores del
DSD aprobado. No introduzcas valores, componentes ni patrones que no estén
documentados en él.

**Oferta de DSD Comprimido:**
Independientemente del Health Score, ofrece siempre:

> ¿Quieres generar también `DESIGN_SYSTEM_MINI.md`?
>
> Es una versión condensada del DSD con solo los tokens esenciales, sin
> descripciones extendidas. Ideal para usarla como contexto en system prompts.

Si el usuario acepta, genera el archivo con este formato (solo los campos que
estén completos — omite los que sean `[pendiente]`):

```
# [Proyecto] — Design System Mini v[versión]

Stack: [framework] + [estilos] + [lenguaje]

Colores:
  primario=[hex] · secundario=[hex] · acento=[hex]
  fondo=[hex] · superficie=[hex] · texto=[hex]
  éxito=[hex] · error=[hex] · advertencia=[hex]

Fuentes: [Fuente principal] ([uso]) · [Fuente secundaria] ([uso])

Escala tipográfica:
  H1=[tamaño]/[peso] · H2=[tamaño]/[peso] · H3=[tamaño]/[peso]
  Body=[tamaño]/[peso] · Small=[tamaño]/[peso]

Espaciado: base=[px] — escala: [4, 8, 12, 16, 24, 32, 48, 64]
Breakpoints: mobile<[px] · tablet=[px]–[px] · desktop>[px]

Componentes: [lista separada por comas]

Restricciones: [restricciones clave del stack y componentes]
```

---

*Frontera V1 — Agnóstico de herramienta. Compatible con cualquier IA.*
