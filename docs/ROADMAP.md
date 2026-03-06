# Frontera — Roadmap

Este documento registra el estado de mejoras al procedimiento, organizadas
entre implementadas y pendientes.

---

## Implementado en V1

| ID | Descripción | Notas de implementación |
|---|---|---|
| V1.1 | Detección de tipo de proyecto | Q3 en Fase 0. El tipo guía los componentes mínimos en Fase 2. |
| V1.2 | Modo Rebranding | Opción 5 en Modo Actualización. Versión sube a major. Preserva §4, §6, §7. |
| V1.3 | DSD Comprimido | Oferta de `DESIGN_SYSTEM_MINI.md` siempre al final de Fase 3. |
| V1.4 | Manejo de equipos (parcial) | Campo `autor` en historial, detectado de `git config user.name`. Reconciliación remota/local queda pendiente. |
| V2.4 | Multi-DSD (base) | Q4 en Fase 0. Schema `dsds[]` en `frontera.json`. Modo Actualización selecciona DSD activo. Propagación entre DSDs queda pendiente. |
| — | Health Score | Diagnóstico de completitud en Fase 3: ✅ = 12.5 · ⚠️ = 6 · ❌ = 0. Las secciones §2, §3, §4 y §8 son críticas. |
| — | Aprobación condicional | Tres caminos según Health Score: completar ahora / aprobar con advertencia / dejar como borrador. |
| — | Oferta de UI Kit | Al aprobar con 100/100, oferta de generar el UI Kit base desde el DSD aprobado. |
| — | Defaults de framework | En E3/E4, cuando no hay escala tipográfica/espaciado personalizada, el procedimiento pregunta y documenta los defaults como `[default nombre-del-framework]`. |

---

## Mejoras a V1 (pendientes)

Estas mejoras no rompen compatibilidad y pueden incorporarse al procedimiento
actual como actualizaciones menores.

---

### V1.1 — Detección inteligente de tipo de proyecto *(implementado)*

**Problema:** Los componentes mínimos por tipo de proyecto (web, dashboard,
móvil, landing) son una heurística fija. El procedimiento no detecta el tipo
automáticamente — lo infiere del contexto o espera que el usuario lo mencione.

**Propuesta:** Añadir en Fase 0 una pregunta de diagnóstico de tipo de proyecto
cuando no es inferible de las respuestas anteriores. La tabla de routing absorbería
esta dimensión sin añadir una nueva entrada.

**Complejidad:** Baja | **Impacto:** Medio

---

### V1.2 — Modo Rebranding *(implementado)*

**Problema:** Existe un escenario real no cubierto por las 4 entradas: el proyecto
tiene un DSD aprobado pero el cliente hace un rebranding completo (nueva paleta,
nueva tipografía, nuevo logo). El Modo Actualización está pensado para cambios
incrementales, no para reemplazos totales de identidad.

**Propuesta:** Añadir una opción 5 en el Modo Actualización: *"El proyecto cambió
de identidad de marca (rebranding)"*. En este caso, la IA mantiene la sección de
Stack, Componentes y Espaciado/Grid del DSD anterior y reemplaza solo las
secciones de identidad y color. La versión sube a major (1.x → 2.0).

**Complejidad:** Baja | **Impacto:** Alto

---

### V1.3 — DSD Comprimido para System Prompts *(implementado)*

**Problema:** El DSD completo puede superar los 2,000 tokens. En proyectos donde
la IA de desarrollo lo recibe como parte de un system prompt junto a otras
instrucciones, esto puede consumir contexto valioso o ser truncado.

**Propuesta:** Añadir en Fase 3 la opción de generar un `DESIGN_SYSTEM_MINI.md`
— una versión comprimida con solo los tokens esenciales (colores hex, fuentes,
escala de espaciado, stack) sin las descripciones extendidas. Orientado
exclusivamente al consumo por IAs, no como documentación humana.

**Complejidad:** Baja | **Impacto:** Alto

---

### V1.4 — Manejo de Equipos *(parcialmente implementado)*

**Problema:** Cuando múltiples personas ejecutan Frontera sobre el mismo proyecto
(ej. un diseñador y un desarrollador), pueden generarse versiones paralelas del
DSD con conflictos entre sí que el procedimiento no detecta.

**Propuesta:** Añadir al schema de `frontera.json` un campo `autor` por entrada
en el historial. Instruir al procedimiento a detectar si la versión local del
`frontera.json` difiere de la versión en el repositorio remoto (si el operador
lo informa) y presentar una tabla de reconciliación antes de continuar.

**Complejidad:** Media | **Impacto:** Medio

---

## Frontera V2

Mejoras que requieren un cambio de versión mayor por su impacto en el flujo
o por introducir dependencias externas opcionales.

---

### V2.1 — Modo Auditoría

**Problema:** Con el tiempo, el código puede divergir del DSD aprobado. Los
desarrolladores añaden colores ad-hoc, usan fuentes no documentadas o crean
componentes fuera del catálogo. No existe un mecanismo para detectar esta deriva.

**Propuesta:** Un nuevo modo de ejecución: *"Auditar el proyecto"*. La IA
escanea el repositorio y el DSD en paralelo, produciendo un reporte de desviaciones:

```
## Reporte de Auditoría — [Proyecto] v1.3
Fecha: 2026-05-01

### Colores no documentados en el DSD
- #2C3E50 usado en src/components/Header.tsx (sin asignar a ningún rol)

### Fuentes no documentadas
- 'Roboto Mono' en src/components/CodeBlock.tsx

### Componentes sin documentar
- <DataGrid> en src/pages/Analytics.tsx
```

El reporte queda en `.frontera/audit-[fecha].md`. El operador decide si actualiza
el DSD o corrige el código.

**Complejidad:** Media | **Impacto:** Muy alto

---

### V2.2 — Integración con Figma API

**Problema:** En Entradas 2 y 4, la extracción de tokens desde imágenes es
imprecisa — los valores hex son aproximados y los nombres de fuentes pueden
fallar. La fuente de verdad real en proyectos con diseñador es Figma.

**Propuesta:** Añadir una Entrada 5 opcional: *"Con archivo de Figma"*. El
operador provee el token de API de Figma y el ID del archivo. La IA extrae
directamente variables, estilos de texto, componentes y grids de la API,
eliminando la imprecisión de la extracción visual.

El procedimiento mantiene la ruta de imágenes como fallback para equipos
sin acceso a Figma.

**Complejidad:** Alta | **Impacto:** Muy alto

---

### V2.3 — GitHub Action: DSD Sentinel

**Problema:** El DSD puede quedar desactualizado silenciosamente. No existe
ningún mecanismo automático que detecte que el código divergió del DSD entre
pull requests.

**Propuesta:** Una GitHub Action que, en cada PR, ejecuta una validación básica:
compara los tokens del `tailwind.config.js` (u otro archivo de tokens) con los
valores documentados en el DSD. Si detecta discrepancias, añade un comentario
al PR solicitando actualizar el DSD o justificar la desviación.

```yaml
# .github/workflows/frontera-sentinel.yml
name: DSD Sentinel
on: [pull_request]
jobs:
  validate-dsd:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Validar coherencia DSD ↔ tokens
        run: # script de comparación
```

**Complejidad:** Alta | **Impacto:** Alto

---

### V2.4 — Multi-DSD por Proyecto *(base implementada)*

**Problema:** Proyectos grandes (ej. plataforma web + app móvil + sistema de
emails) tienen sistemas de diseño parcialmente compartidos y parcialmente
independientes. Un único `DESIGN_SYSTEM.md` no puede representar bien esta
separación sin volverse inmanejable.

**Propuesta:** Soporte para múltiples DSDs coordinados. El `frontera.json`
evolucionaría para registrar varios documentos:

```json
{
  "dsds": [
    { "id": "web", "location": "docs/DESIGN_SYSTEM_WEB.md", "version": "2.1" },
    { "id": "mobile", "location": "docs/DESIGN_SYSTEM_MOBILE.md", "version": "1.3" },
    { "id": "shared", "location": "docs/DESIGN_SYSTEM_SHARED.md", "version": "2.1" }
  ]
}
```

La Fase 0 preguntaría qué DSD ejecutar o actualizar, con la opción de propagar
cambios del DSD `shared` a los demás.

**Complejidad:** Alta | **Impacto:** Alto

---

## Ecosistema

Herramientas externas al procedimiento que ampliarían su utilidad.

---

### ECO-1 — Frontera CLI

**Problema:** La preparación manual de `.frontera/` (crear carpetas, nombrar
logos correctamente) es un paso repetitivo propenso a errores.

**Propuesta:** Una herramienta de línea de comandos:

```bash
npx frontera init          # crea .frontera/ con la estructura correcta
npx frontera status        # muestra el estado del DSD (versión, estado, última ejecución)
npx frontera history       # imprime el historial de frontera.json
npx frontera validate      # valida que DESIGN_SYSTEM.md cumple el schema
```

**Complejidad:** Media | **Impacto:** Medio

---

### ECO-2 — DSD Linter

**Problema:** Un `DESIGN_SYSTEM.md` generado por la IA puede tener campos
`[pendiente]` olvidados, filas vacías en tablas o inconsistencias entre el
frontmatter y el contenido.

**Propuesta:** Un script de validación que verifica:
- Todos los campos del frontmatter están completos
- No hay celdas de tabla vacías (excepto las opcionales documentadas)
- No hay campos `[pendiente]` si el estado es `aprobado`
- Los hex en la tabla de colores son válidos (#RRGGBB o #RGB)
- `dsd_version` en frontmatter coincide con `dsd_version` en `frontera.json`

**Complejidad:** Baja | **Impacto:** Medio

---

### ECO-3 — Exportador de Tokens

**Problema:** El DSD tiene una sección 8 (Tokens de Implementación) que la IA
genera en el formato del stack. Sin embargo, cuando el stack cambia o el DSD
se actualiza, la sección 8 puede quedar desincronizada.

**Propuesta:** Un script que lee las secciones 2, 3 y 4 del DSD y genera
automáticamente la sección 8 en el formato correcto para el stack declarado
en la sección 7, sin depender de la IA.

**Complejidad:** Media | **Impacto:** Alto

---

## Tabla Resumen

| ID | Descripción | Complejidad | Impacto | Estado |
|---|---|---|---|---|
| V1.1 | Detección de tipo de proyecto | Baja | Medio | ✅ Implementado |
| V1.2 | Modo Rebranding | Baja | Alto | ✅ Implementado |
| V1.3 | DSD Comprimido para system prompts | Baja | Alto | ✅ Implementado |
| V1.4 | Manejo de equipos | Media | Medio | ⚠️ Parcial (autor vía git) |
| V2.1 | Modo Auditoría | Media | Muy alto | Pendiente V2 |
| V2.2 | Integración Figma API | Alta | Muy alto | Pendiente V2 |
| V2.3 | GitHub Action: DSD Sentinel | Alta | Alto | Pendiente V2 |
| V2.4 | Multi-DSD por proyecto | Alta | Alto | ⚠️ Base implementada |
| ECO-1 | Frontera CLI | Media | Medio | Pendiente |
| ECO-2 | DSD Linter | Baja | Medio | Pendiente |
| ECO-3 | Exportador de Tokens | Media | Alto | Pendiente |
