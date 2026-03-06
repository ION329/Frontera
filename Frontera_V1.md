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
Dale el contenido de este archivo a una IA según tu entorno:
- **Web** (Claude.ai, ChatGPT, Gemini) → sube `.frontera/Frontera_V1.md` como adjunto, o pega su contenido completo en el chat
- **IDE** (Claude Code, Cursor) → referencia el archivo en tu mensaje: `@.frontera/Frontera_V1.md`
- **API** → pega este contenido en el system prompt

Luego dile:
> *"Ejecuta el procedimiento Frontera V1."*

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

Según las respuestas, determina el punto de entrada y avanza a Fase 1:

| Condición | Entrada |
|---|---|
| Sin código, sin diseño | 1 |
| Con diseño, sin código | 2 |
| Con código, sin diseño | 3 |
| Con código y con diseño | 4 |

---

## Modo Actualización

*Se activa cuando existe `.frontera/frontera.json`.*

**Paso 1 — Leer el registro y presentar el estado actual:**
Con los datos del JSON, envía este resumen al usuario:

> Encontré el registro de Frontera para este proyecto:
>
> - **Proyecto:** [proyecto]
> - **DSD:** [dsd_location] — v[dsd_version] ([dsd_status])
> - **Última ejecución:** [ultima_ejecucion]
> - **Historial:** [N] ejecuciones previas
>
> ¿Qué quieres actualizar?
>
> 1. Llegaron nuevos archivos de diseño en `.frontera/mockups/`
> 2. Hubo cambios específicos (colores, tipografía, componentes, etc.)
> 3. El código evolucionó — hay componentes nuevos o modificados
> 4. Revisión general — quiero mejorar o completar secciones incompletas
>
> Puedes combinar varias opciones.

**Paso 2 — Cargar el DSD:**
Accede al DSD en la ruta indicada por `dsd_location`. Si no tienes acceso
al sistema de archivos, pide al usuario que lo comparta.

**Paso 3 — Extracción del delta:**
Según la respuesta del usuario, extrae solo lo que cambió:
- Opción 1 → Analiza las nuevas imágenes en `.frontera/mockups/`
- Opción 2 → Pregunta exactamente qué valores cambiaron y cuáles son los nuevos
- Opción 3 → Pide los archivos de código nuevos o modificados y extrae tokens y patrones
- Opción 4 → Revisa el DSD sección por sección e identifica campos `[pendiente]` o inconsistencias

**Paso 4 — Presentar el diff:**
Antes de aplicar cualquier cambio, presenta una tabla con todo lo que va a modificarse:

| Sección | Campo | Valor actual | Valor propuesto |
|---|---|---|---|
| [ej. Sistema de Color] | [ej. Primario Hex] | [valor actual] | [valor nuevo] |

Espera aprobación. Si el usuario rechaza algún cambio, retíralo antes de continuar.

**Paso 5 — Aplicar y versionar:**
- Aplica únicamente los cambios aprobados en el DSD
- Incrementa la versión menor: `1.0 → 1.1`, `1.1 → 1.2`, etc.
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

Regla: no inventes valores que no estén en el código.

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
Añade los fundamentales según el tipo de proyecto:
- Interfaz web → Button, Input
- Dashboard → Button, Input, Card, Table
- App móvil → Button, Input, NavigationBar
- Landing estática → Button, Hero, Footer

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

Después de entregar el DSD, presenta este mensaje al usuario:

> El DSD está generado (v[X.X]). Antes de aprobarlo, revisa:
>
> - [ ] **Colores:** ¿Los valores hex son exactos o aproximaciones?
> - [ ] **Tipografía:** ¿Las fuentes nombradas son las reales del proyecto?
> - [ ] **Logos:** ¿Las variantes documentadas son las correctas?
> - [ ] **Stack:** ¿El stack está completo y correcto?
> - [ ] **Tokens de implementación:** ¿El formato es el correcto para tu stack?
> - [ ] **Pendientes:** ¿Hay campos marcados como `[pendiente]`?
> - [ ] **Conflictos (si usaste Entrada 4):** ¿Todos fueron resueltos?
>
> ¿Dónde quieres guardar el `DESIGN_SYSTEM.md`? Opciones:
> - **Raíz del proyecto** — máxima visibilidad, fácil de encontrar
> - **`/docs/`** — si el proyecto ya tiene carpeta de documentación
> - **`.frontera/`** — junto a los assets de diseño

**Ciclo de corrección:** Si el usuario indica correcciones, actualiza únicamente
los campos señalados y re-presenta el checklist. Repite hasta recibir aprobación
explícita.

**Al aprobar:**
1. Actualiza `estado: borrador → aprobado` en el frontmatter del DSD.
2. Crea o actualiza `.frontera/frontera.json` con esta estructura:

```json
{
  "procedimiento": "Frontera V1",
  "proyecto": "[nombre del proyecto]",
  "dsd_location": "[ruta relativa al DESIGN_SYSTEM.md]",
  "dsd_version": "[versión aprobada]",
  "dsd_status": "aprobado",
  "creado": "[fecha de la primera ejecución]",
  "ultima_ejecucion": "[fecha actual]",
  "historial": [
    {
      "fecha": "[fecha]",
      "modo": "creacion | actualizacion",
      "entrada": "[1 | 2 | 3 | 4 | actualizacion]",
      "version_resultante": "[versión]",
      "resumen": "[descripción breve de lo que se generó o cambió]"
    }
  ]
}
```

Si el archivo ya existe, agrega la nueva entrada al array `historial` sin
eliminar las anteriores. Actualiza `dsd_version`, `dsd_status` y `ultima_ejecucion`.

Si no tienes acceso al sistema de archivos, muestra el JSON generado al usuario
e indícale que lo guarde como `.frontera/frontera.json`.

---

*Frontera V1 — Agnóstico de herramienta. Compatible con cualquier IA.*
