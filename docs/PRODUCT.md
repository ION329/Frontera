# Frontera — Documentación de Producto

## Visión

Frontera es un procedimiento portable para establecer el sistema de diseño de
cualquier proyecto de software, en cualquier momento de su ciclo de vida.
Su propósito es producir un documento estructurado — el DSD — que actúe como
fuente de verdad única, legible tanto por desarrolladores humanos como por
inteligencias artificiales participantes en el desarrollo.

---

## El Problema

Los equipos de desarrollo enfrentan un problema recurrente: las decisiones
visuales y técnicas que definen la identidad de un producto no están documentadas
de forma que sea consumible por todos los participantes del proyecto.

Esto genera tres síntomas concretos:

1. **Deriva visual** — Cada desarrollador o IA interpreta los colores, fuentes y
   espaciados de forma diferente. El resultado es una interfaz incoherente.

2. **Conocimiento implícito** — Los lineamientos de diseño viven en la cabeza
   del diseñador o en un archivo de Figma que no todos pueden leer.

3. **Fricción en IA-assisted development** — Las IAs de desarrollo no tienen
   instrucciones formales de diseño. Sin restricciones explícitas, alucinan
   estilos, inventan colores y producen componentes inconsistentes.

Frontera resuelve los tres documentando el sistema de diseño de forma
estructurada, completa y accesible antes — o en paralelo — al desarrollo.

---

## Usuarios Objetivo

**Desarrolladores humanos**
Necesitan una referencia clara y actualizada que elimine la ambigüedad al
construir interfaces. El DSD les dice qué colores usar, qué tipografía aplicar
y cómo estructurar los componentes, sin depender de que el diseñador esté
disponible.

**IAs de desarrollo (Claude, GPT, Gemini, Cursor, etc.)**
Necesitan instrucciones en texto plano que puedan consumir como contexto.
El DSD está diseñado para ser compartido directamente con cualquier IA como
parte de un system prompt o como archivo de contexto, restringiendo su libertad
creativa a los lineamientos definidos.

**Diseñadores y Product Managers**
Necesitan un artefacto que capture las decisiones de diseño tomadas — muchas
veces verbalmente o en mockups — y las formalice en un documento versionado
y rastreable.

---

## Conceptos Clave

**DSD — Documento del Sistema de Diseño**
El output principal del procedimiento. Un archivo Markdown estructurado con
YAML frontmatter que contiene identidad de marca, paleta de colores, tipografía,
espaciado, componentes y stack tecnológico. Vive en el repositorio del proyecto.

**`.frontera/`**
Carpeta en la raíz del proyecto que centraliza los assets de diseño:
logos en sus variantes, mockups de referencia y el registro del procedimiento.

**`frontera.json`**
Registro de estado del proyecto. Almacena la ubicación del DSD, su versión
actual, el historial de ejecuciones y el estado de aprobación. Permite a
cualquier IA retomar el contexto de ejecuciones anteriores sin preguntar al usuario.

**Entradas (1–4)**
Los cuatro puntos de entrada al procedimiento según el estado del proyecto:
desde cero, con diseño existente, con código existente, o con ambos.

**Modo Actualización**
Flujo alternativo que se activa cuando ya existe un DSD. En lugar de generar
desde cero, extrae solo el delta, presenta un diff al usuario y versiona
incrementalmente.

---

## Propuesta de Valor

| Característica | Beneficio |
|---|---|
| Procedimiento en un solo archivo `.md` | Portable — llega por email, WhatsApp, repositorio o cualquier canal |
| Agnóstico de herramienta | Funciona con cualquier IA sin dependencias de plataforma |
| 4 puntos de entrada | Cubre todos los estados reales de un proyecto |
| Modo Actualización con diff | No descarta trabajo previo — actualiza quirúrgicamente |
| `frontera.json` como registro | La IA retoma el contexto sin preguntar al usuario |
| Validación humana obligatoria | El operador aprueba antes de que el DSD sea oficial |
| Anti-alucinación por diseño | Reglas explícitas evitan que la IA invente valores |
| DSD en texto plano y Markdown | Versionable con git, legible sin herramientas especiales |

---

## Ciclo de Vida del DSD

```
Creación (Entradas 1–4)
       ↓
  Aprobación (Fase 3)
       ↓
   Uso activo
  (desarrollo)
       ↓
  Cambios / nuevos
   diseños / código
       ↓
Modo Actualización
       ↓
  Re-aprobación
       ↓
   Uso activo
  (iteración)
```

El DSD no es un documento estático. Cada vez que el sistema de diseño evoluciona
— nuevos componentes, paleta actualizada, cambio de stack — se ejecuta Frontera
en Modo Actualización. La versión se incrementa, el historial se preserva.

---

## Versiones del Procedimiento

### Frontera V1 *(actual)*
Primera versión estable. Diseñada como procedimiento agnóstico de herramienta,
portable en un único archivo `.md`. Cubre creación y actualización del DSD con
4 puntos de entrada y registro de historial.

### SOP Frontera V2 *(archivado)*
Versión anterior. Diseñada para un stack específico de herramientas (NotebookLM,
Stitch, Google Antigravity). Dependía de integraciones de plataforma que no
estaban verificadas. Reemplazada por V1 por ser más simple, portable y libre
de dependencias externas.

> La numeración es inversa por diseño: la versión más simple y robusta recibió
> el número 1. Las versiones futuras (V2, V3) añadirán capacidades sobre esta base.

---

## Dirección Futura

Las mejoras identificadas están organizadas en tres categorías:
mejoras a V1 (sin romper compatibilidad), funcionalidades para V2, y
herramientas del ecosistema.

Ver el detalle completo en [ROADMAP.md](ROADMAP.md).

**Highlights de mayor impacto:**
- **Modo Auditoría (V2.1)** — Detecta desviaciones entre el DSD aprobado y el código actual
- **Integración Figma API (V2.2)** — Extracción exacta de tokens sin depender de análisis visual
- **DSD Comprimido (V1.3)** — Versión minificada del DSD optimizada para system prompts
- **Modo Rebranding (V1.2)** — Flujo para reemplazos totales de identidad de marca
- **GitHub Action DSD Sentinel (V2.3)** — Valida coherencia DSD ↔ código en cada PR
