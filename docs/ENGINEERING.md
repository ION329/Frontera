# Frontera — Documentación de Ingeniería

## Arquitectura General

`Frontera_V1.md` es un archivo de doble audiencia: la primera sección está
escrita para el operador humano; el resto está escrito para ser ejecutado por
una IA. Ambas audiencias leen el mismo archivo, pero consumen secciones distintas.

```
Frontera_V1.md
│
├── ## Guía de Uso          ← Lee el humano. Instrucciones de preparación y ejecución.
├── <!-- comentario HTML --> ← Separador visual. Las IAs lo leen; los renderers lo ocultan.
├── Instrucción de rol      ← La IA toma el control a partir de aquí.
├── ## Fase 0               ← Lógica de routing (creación vs. actualización)
├── ## Modo Actualización   ← Flujo alternativo cuando DSD existe
├── ## Fase 1               ← Extracción (4 entradas)
├── ## Fase 2               ← Generación del DSD
└── ## Fase 3               ← Validación + ciclo de corrección + escritura de frontera.json
```

**Por qué un solo archivo:**
La portabilidad es el atributo más importante del procedimiento. Un archivo que
viaja por email o WhatsApp debe contener todo — instrucciones para el humano,
instrucciones para la IA, y la plantilla del DSD. Dividirlo en múltiples archivos
rompería la portabilidad.

---

## Flujo de Ejecución

```
[Usuario comparte Frontera_V1.md con cualquier IA]
              │
              ▼
       Fase 0: Diagnóstico
              │
    ¿Existe .frontera/frontera.json?
         │              │
        SÍ              NO
         │              │
         ▼              ▼
   Modo              Preguntas de
Actualización        diagnóstico
         │          (2 preguntas)
         │              │
         │         Routing matrix
         │         (Entradas 1–4)
         │              │
         └──────┬────────┘
                │
                ▼
          Fase 1: Extracción
          (según la entrada)
                │
                ▼
          Fase 2: Generación
          del DSD completo
                │
                ▼
          Fase 3: Validación
          + ciclo corrección
                │
          ¿Aprobado?
         │              │
        NO              SÍ
         │              │
    Corrección    estado: aprobado
    + checklist   Escribe frontera.json
                        │
                        ▼
                  [DSD guardado]
```

---

## Decisiones de Diseño

### Por qué agnóstico de herramienta
La versión anterior (SOP_Frontera_V2) dependía de NotebookLM, Stitch y
Google Antigravity — herramientas cuya disponibilidad, API y comportamiento
no podíamos controlar. Un procedimiento basado en prompts de texto funciona
con cualquier LLM y no tiene fecha de expiración.

### Por qué la detección usa `frontera.json` y no buscar `DESIGN_SYSTEM.md`
El DSD puede estar en la raíz, en `/docs/` o en `.frontera/` — tres ubicaciones
posibles. Buscar el archivo requeriría tres intentos o preguntar al usuario.
`frontera.json` siempre está en `.frontera/` y contiene la ruta exacta del DSD.
Un solo punto de lookup, sin ambigüedad.

### Por qué el diagnóstico tiene exactamente 4 entradas
El espacio de estados posibles al iniciar un proyecto tiene dos dimensiones
binarias independientes: {tiene código / no tiene código} × {tiene diseño / no tiene diseño}.
Cuatro estados, cuatro entradas. Añadir más subdivisiones aumenta la complejidad
sin resolver problemas reales de extracción.

### Por qué el stack se pregunta en Fase 1 y no en Fase 0
En Fase 0 solo se necesita saber si hay código o diseño para determinar el
punto de entrada. El stack no afecta ese routing. Pedirlo en Fase 0 sería
una pregunta cuya respuesta no se usaría hasta después, generando fricción
innecesaria. En Fase 1, cada entrada captura el stack de la forma más natural
según su contexto (preguntando en Entrada 1, infiriendo en Entrada 2, extrayendo
del código en Entradas 3 y 4).

### Por qué las preguntas de Entrada 1 se dividen en 2 bloques
Un solo bloque de 7 preguntas produce respuestas superficiales — el usuario
las responde como una lista, sin reflexionar en cada una. Dos bloques temáticos
(Marca / Interfaz+Tecnología) permiten que el usuario se enfoque en un dominio
antes de pasar al siguiente, produciendo respuestas más completas.

### Por qué la reconciliación de Entrada 4 es obligatoria
Cuando coexisten código y diseño nuevo, los conflictos son casi inevitables
(una paleta que evolucionó, un espaciado que cambió). Permitir que la IA
elija qué prevalece sin consultar al usuario produciría un DSD con decisiones
implícitas que nadie aprobó. La tabla de reconciliación fuerza una decisión
explícita antes de avanzar.

### Por qué el Modo Actualización presenta un diff antes de aplicar
Un DSD aprobado es un documento de consenso. Aplicar cambios directamente
— incluso cambios derivados de materiales que el propio usuario compartió —
rompe ese contrato de aprobación. El diff garantiza que el usuario ve y aprueba
exactamente qué va a cambiar antes de que el estado del DSD se modifique.

### Por qué la versión solo incrementa el minor en actualizaciones
El major version bump (1.x → 2.0) indica un cambio de identidad o de
arquitectura del sistema de diseño que merece ser marcado manualmente por
el equipo. Los cambios ordinarios — nuevos componentes, colores ajustados,
stack actualizado — incrementan el minor (1.0 → 1.1). Esta convención
refleja semver de forma intuitiva sin requerir que el usuario conozca semver.

---

## Estrategias Anti-Alucinación

Las IAs, sin restricciones, inventan valores. Frontera implementa cuatro
mecanismos explícitos para prevenirlo:

**1. Prohibición de invención**
La instrucción `"no inventes valores que no estén en el código"` en Entradas 3 y 4
es una directiva directa. Las IAs respetan prohibiciones explícitas mejor que
restricciones implícitas.

**2. Marcador `[pendiente]`**
En lugar de dejar un campo vacío (que la IA puede rellenar con un valor
plausible) o inventar un valor, se usa `[pendiente]`. Esto hace visible la
ausencia de datos y la transfiere al checklist de Fase 3.

**3. Tabla de reconciliación como gate humano**
La reconciliación en Entrada 4 detiene la ejecución hasta que el humano
decide explícitamente. Es imposible que la IA avance con un conflicto sin
resolución — el procedimiento lo requiere estructuralmente.

**4. Checklist de validación en Fase 3**
Los campos más susceptibles a alucinación (hex colors, nombres de fuentes)
tienen ítems explícitos en el checklist. El operador no puede aprobar el DSD
sin haberse detenido a verificarlos.

---

## Schema: `frontera.json`

```json
{
  "procedimiento": "Frontera V1",
  "proyecto": "string — nombre del proyecto",
  "dsd_location": "string — ruta relativa al DESIGN_SYSTEM.md desde la raíz del proyecto",
  "dsd_version": "string — versión semver simplificada, ej. '1.0', '1.3'",
  "dsd_status": "string — 'borrador' | 'aprobado'",
  "creado": "string — fecha ISO 8601 de la primera ejecución (YYYY-MM-DD)",
  "ultima_ejecucion": "string — fecha ISO 8601 de la última ejecución",
  "historial": [
    {
      "fecha": "string — YYYY-MM-DD",
      "modo": "string — 'creacion' | 'actualizacion'",
      "entrada": "string — '1' | '2' | '3' | '4' | 'actualizacion'",
      "version_resultante": "string — versión del DSD producida en esta ejecución",
      "resumen": "string — descripción breve de lo generado o modificado"
    }
  ]
}
```

**Reglas de escritura:**
- `creado` se establece en la primera ejecución y nunca se modifica.
- `ultima_ejecucion` se actualiza en cada aprobación.
- `historial` es append-only. No se eliminan entradas anteriores.
- `dsd_status` vuelve a `borrador` al iniciar cualquier actualización
  y regresa a `aprobado` al completar la validación.

---

## Schema: DSD (`DESIGN_SYSTEM.md`)

### YAML Frontmatter

| Campo | Tipo | Descripción |
|---|---|---|
| `proyecto` | string | Nombre del proyecto |
| `version` | string | Versión semver simplificada (`1.0`, `1.1`, etc.) |
| `fecha` | string | Fecha de última actualización en `YYYY-MM-DD` |
| `estado` | enum | `borrador` durante el proceso, `aprobado` al completar Fase 3 |
| `generado-con` | string | Siempre `Frontera V1` — identifica el procedimiento origen |

### Secciones del documento

| Sección | Propósito |
|---|---|
| 1. Identidad de Marca | Ancla la identidad del proyecto. Incluye logos con variantes y rutas. |
| 2. Sistema de Color | Tokens de color con roles semánticos, nombres, hex y uso. |
| 3. Tipografía | Escala tipográfica con tokens, tamaños, pesos y line-heights. |
| 4. Espaciado y Grid | Sistema de espaciado, grid de columnas y breakpoints. |
| 5. Tono y Principios de UI | Reglas que guían decisiones visuales ambiguas. |
| 6. Componentes | Catálogo de componentes con estructura, variantes, estados y restricciones. |
| 7. Stack Tecnológico | Framework, lenguaje, herramientas de estilo y restricciones técnicas. |
| 8. Tokens de Implementación | Tokens en el formato nativo del stack (Tailwind, CSS vars, JSON). Opcional. |

---

## Limitaciones Conocidas

**Aproximación de colores en imágenes**
Al analizar mockups (Entradas 2 y 4), las IAs aproximan los valores hex a partir
de la imagen. Esta aproximación puede tener un margen de error. El checklist
de Fase 3 incluye verificación de hex explícitamente para mitigarlo.

**Identificación tipográfica imprecisa**
Las IAs no pueden identificar con certeza el nombre exacto de una fuente a
partir de una imagen. En proyectos con Entrada 2, el nombre de la fuente debe
ser confirmado por el operador en Fase 3.

**Sin acceso a sistema de archivos en interfaces web**
En Claude.ai, ChatGPT u otras interfaces web, la IA no puede leer ni escribir
archivos. En estos entornos, el operador debe compartir manualmente el contenido
de `frontera.json` y guardar los archivos generados. El procedimiento lo
contempla y adapta las instrucciones según el contexto.

**Contexto largo en Modo Actualización**
Si el DSD es extenso y el `frontera.json` tiene un historial largo, la conversación
puede consumir una porción significativa del contexto disponible de la IA antes
de comenzar la extracción del delta. En esos casos, compartir solo las secciones
relevantes del DSD mejora el resultado.

**Stack no definido**
Cuando el stack no está decidido, la sección 8 (Tokens de Implementación) se
omite completamente. Esto es correcto — no es posible generar tokens en un
formato no conocido. Sin embargo, si el stack se define después, el operador
debe volver a ejecutar Frontera para generar esa sección.

---

## Guía para Evolucionar el Procedimiento

### Lo que no debe cambiar
- El diseño de doble audiencia (guía humana + instrucciones IA en un archivo)
- El sistema de 4 entradas basado en {código, diseño}
- La obligatoriedad de la reconciliación en Entrada 4
- Los mecanismos anti-alucinación (`[pendiente]`, prohibición de invención, checklist)
- El schema de `frontera.json` (solo extender, nunca romper campos existentes)

### Cómo añadir una nueva Entrada
1. Identificar el nuevo estado del proyecto que no cubre ninguna entrada existente.
2. Definir qué materiales necesita el operador preparar.
3. Definir el proceso de extracción (qué extrae la IA y qué pregunta).
4. Actualizar la routing matrix en Fase 0 con la nueva condición.
5. Documentar la nueva entrada en Fase 1.

### Cómo añadir campos al DSD
1. Añadir el campo a la plantilla en Fase 2 con un placeholder `[descripción]`.
2. Añadir la pregunta o extracción correspondiente en la(s) Entrada(s) relevantes.
3. Añadir un ítem al checklist de Fase 3 si el campo es susceptible a alucinación.
4. Documentar el nuevo campo en la tabla de schemas de este archivo.

### Cómo añadir campos a `frontera.json`
1. Añadir el campo a la instrucción de escritura en Fase 3.
2. Documentar el campo en la tabla de schema de este archivo.
3. Asegurar que el Modo Actualización lee y preserva el nuevo campo.
4. El campo debe ser opcional o tener un valor por defecto para no romper
   registros existentes de versiones anteriores.

---

## Changelog

### Frontera V1 (2026-03-06)
Primera versión estable del procedimiento.

**Cambios respecto a SOP_Frontera_V2:**
- Eliminada la dependencia de herramientas específicas (NotebookLM, Stitch, Antigravity)
- El procedimiento es ahora un único archivo `.md` portable
- Rediseño como instrucción directa para la IA (en lugar de documentación sobre prompts)
- Añadidas las 4 entradas basadas en el estado real del proyecto
- Añadido Modo Actualización con diff y versionado incremental
- Añadido `frontera.json` como registro de estado persistente
- Añadidas estrategias explícitas anti-alucinación
- Añadida sección de logos con convención de nombres en `.frontera/logos/`
- Añadida sección de Tokens de Implementación adaptada al stack
- Añadido ciclo de corrección en Fase 3
- Eliminada la promesa de pixel-perfect (no garantizable)
- Eliminada la integración MCP (depende de herramientas no verificadas)
