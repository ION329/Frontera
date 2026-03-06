# Frontera

[![License: MIT](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)
[![Version](https://img.shields.io/badge/version-V1-green?style=flat-square)](Frontera_V1.md)
[![AI Agnostic](https://img.shields.io/badge/IA-agnóstico-purple?style=flat-square)](#compatibilidad)

**Procedimiento portable para generar el sistema de diseño de cualquier proyecto, con cualquier IA.**

Frontera es un archivo `.md` que le indica a cualquier IA cómo producir tu **Documento del Sistema de Diseño (DSD)** — la fuente de verdad visual y técnica para desarrolladores humanos y agentes de IA participantes en el desarrollo.

---

## Inicio Rápido

1. Crea la carpeta `.frontera/` en la raíz de tu proyecto
2. Descarga [`Frontera_V1.md`](Frontera_V1.md) y colócalo dentro de `.frontera/`
3. Inicia la conversación con la IA según tu entorno:

| Entorno | Qué hacer |
|---|---|
| Claude.ai, ChatGPT, Gemini (web) | Adjunta `.frontera/Frontera_V1.md` al chat y escribe en el mismo mensaje: *"Ejecuta el procedimiento Frontera V1."* |
| Claude Code, Cursor (IDE) | Escribe en el chat: `@.frontera/Frontera_V1.md ejecuta el procedimiento Frontera V1` |
| API / system prompt | Pega el contenido de `.frontera/Frontera_V1.md` en el system prompt y envía como primer mensaje: *"Ejecuta el procedimiento Frontera V1."* |

La IA guía el resto. Solo tendrás que responder preguntas y aprobar el resultado.

---

## ¿Cómo Funciona?

Frontera diagnostica el estado de tu proyecto y elige el flujo correcto:

| Estado del proyecto | Flujo |
|---|---|
| Sin código, sin diseño | Cuestionario guiado en 2 bloques |
| Con mockups o diseños existentes | Extracción y análisis de imágenes |
| Con código existente | Extracción de tokens y patrones del repositorio |
| Con código + nuevos diseños | Reconciliación con tabla de conflictos |
| DSD ya existe | Modo Actualización — diff + versionado incremental |

---

## Output

Al completar el procedimiento, tu proyecto tiene:

```
proyecto/
├── DESIGN_SYSTEM.md        ← el DSD aprobado (ubicación elegida por el operador)
└── .frontera/
    ├── Frontera_V1.md      ← el procedimiento
    ├── frontera.json       ← registro de ejecuciones e historial
    ├── logos/              ← assets de logo por variante
    │   ├── logo-color.[ext]
    │   ├── logo-white.[ext]
    │   └── logo-black.[ext]
    └── mockups/            ← referencias de diseño
```

`frontera.json` permite que cualquier IA retome el contexto de ejecuciones
anteriores sin preguntar al usuario dónde está el DSD ni qué versión tiene.

---

## Compatibilidad

Frontera es agnóstico de herramienta. Funciona con cualquier LLM:

- Claude (Anthropic)
- ChatGPT (OpenAI)
- Gemini (Google)
- Cualquier IA con capacidad de seguir instrucciones en texto

No requiere integraciones, APIs ni plataformas externas.

---

## Estructura del Repositorio

```
frontera/
├── Frontera_V1.md      ← el procedimiento (este es el archivo que se distribuye)
├── README.md
└── docs/
    ├── PRODUCT.md      ← visión, usuarios, propuesta de valor, roadmap
    └── ENGINEERING.md  ← arquitectura, decisiones de diseño, schemas, changelog
```

---

## Documentación

- [PRODUCT.md](docs/PRODUCT.md) — qué es Frontera, por qué existe y hacia dónde va
- [ENGINEERING.md](docs/ENGINEERING.md) — arquitectura, decisiones técnicas, schemas de `frontera.json` y el DSD, guía para extender el procedimiento
- [ROADMAP.md](docs/ROADMAP.md) — mejoras futuras con descripción, complejidad e impacto
