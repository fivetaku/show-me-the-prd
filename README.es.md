[English](README.md) | [한국어](README.ko.md) | [中文](README.zh.md) | [日本語](README.ja.md) | Español

# show-me-the-prd

<p align="center">
  <img src="assets/show-me-the-prd-hero-01.png" alt="show-me-the-prd" width="320">
</p>

> **Generador de PRD por entrevista para vibe coders — de una sola frase a 4 documentos de diseño.**

Convierte una idea en un plan de producto completo. Responde 5–6 preguntas estructuradas y obtén cuatro documentos de diseño listos para entregar a cualquier herramienta de programación con IA.

[Inicio rápido](#inicio-rápido) • [Por qué show-me-the-prd](#por-qué-show-me-the-prd) • [Cómo funciona](#cómo-funciona) • [Funcionalidades](#funcionalidades) • [Comandos](#comandos) • [Requisitos](#requisitos)

---

## Inicio rápido

### 1. Añade el marketplace

```
/plugin marketplace add https://github.com/fivetaku/gptaku_plugins.git
```

### 2. Instala el plugin

```
/plugin install show-me-the-prd
```

### 3. Reinicia Claude Code

El plugin se carga al iniciar la sesión — reinicia una vez tras instalarlo.

### 4. Empieza a planificar

```
/show-me-the-prd I want to build a photo organization app
```

O simplemente dilo con naturalidad:

```
Make me a PRD for a task manager
```

---

## Por qué show-me-the-prd

- **No hace falta experiencia en planificación** — Cada pregunta viene con explicaciones en lenguaje llano y sus contrapartidas, sin jerga
- **La IA lidera, tú decides** — La IA investiga las opciones en tiempo real y te las presenta como alternativas; nunca tendrás que buscar nada en Google
- **Respaldado por investigación, no por conjeturas** — Búsquedas web en vivo alimentan la lista de funcionalidades, la comparación de stacks y las valoraciones de complejidad
- **Cuatro documentos, no uno** — El PRD, el modelo de datos, el plan por fases y la especificación de proyecto para IA se generan juntos como un conjunto coherente
- **Orientado a producción** — Cada fase apunta a un despliegue real con autenticación real y una base de datos real, no a un mock local
- **Los planes existentes son bienvenidos** — Comparte una especificación que ya tengas; el plugin identifica los huecos y los rellena

---

## Cómo funciona

```
One-sentence idea
       |
       v
  [ Interview ]  ←── live web research runs between each question
       |
   Turn 1: clarify the idea (1–3 questions)
   Turn 2: pick core features + MVP scope
   Turn 3: confirm data model
   Turn 4: confirm phase breakdown
   Turn 5: choose tech stack + auth method
       |
       v
  [ 4 Documents ]
       |
       +── PRD/01_PRD.md            What you're building and who it's for
       +── PRD/02_DATA_MODEL.md     Core data structure (conceptual ERD)
       +── PRD/03_PHASES.md         Phase plan with start prompts
       +── PRD/04_PROJECT_SPEC.md   AI rules + "never do this" list
       +── PRD/README.md            Navigation guide
```

Cada opción de la entrevista incluye una descripción en lenguaje llano, ventajas, inconvenientes y una valoración de complejidad (Simple / Moderate / Complex). Si dudas, elige la marcada como **(recommended)**.

---

## Funcionalidades

### Planificación basada en entrevista

No se requiere vocabulario de desarrollador. En lugar de "elige tu estrategia de autenticación", pregunta "¿cómo deberían iniciar sesión los usuarios?" con opciones concretas como "inicio de sesión social (Google/Kakao)" y "correo + contraseña".

### Investigación web en tiempo real

Entre cada turno de la entrevista, el plugin ejecuta búsquedas en vivo para encontrar funcionalidades relevantes, errores comunes y los stacks de mejores prácticas actuales. Lo que ves en las opciones se basa en resultados reales, no en sugerencias predefinidas.

### Guía de complejidad

Cada funcionalidad del paso de selección lleva su etiqueta:

| Etiqueta | Significado |
|-------|---------|
| Simple | Funciona de serie con la mayoría de frameworks |
| Moderate | Requiere un servicio externo; probable algún coste |
| Complex | Se recomienda omitirla en la primera versión |

### Cuatro documentos de salida

| Documento | Contenido | Cuándo usarlo |
|----------|----------|-------------|
| `01_PRD.md` | Objetivos del producto, historias de usuario, lista de funcionalidades | Compártelo al inicio del proyecto |
| `02_DATA_MODEL.md` | Entidades principales y sus relaciones | Compártelo al diseñar la base de datos |
| `03_PHASES.md` | Plan fase a fase con prompts de arranque | Referencia para el orden de construcción |
| `04_PROJECT_SPEC.md` | Reglas de comportamiento de la IA + lista de "nunca hagas esto" | Compártelo con la IA en cada sesión |

### Modo de mejora

Si ya tienes una especificación o notas, compártelas antes de ejecutar el comando. El plugin las lee, identifica qué falta respecto al estándar de cuatro documentos y ejecuta solo las preguntas necesarias para rellenar los huecos.

---

## Comandos

| Comando | Descripción |
|---------|-------------|
| `/show-me-the-prd [idea]` | Inicia la entrevista con tu idea incluida |
| `/show-me-the-prd` | Empieza con una pregunta inicial interactiva |

### Disparadores en lenguaje natural

El plugin también se activa cuando dices:

- "Hazme un PRD"
- "Crea un documento de planificación"
- "Show me the PRD"
- "Planifica esta app por mí"
- "Planifica [idea] por mí"

---

## Requisitos

- [Claude Code](https://docs.anthropic.com/claude-code) CLI

### Plugins opcionales (recomendados)

Instalarlos junto a show-me-the-prd mejora la calidad de la investigación:

| Plugin | Qué aporta |
|--------|-------------|
| `docs-guide` | Consulta de documentación oficial durante la investigación del stack |
| `insane-research` | Análisis más profundo de mercado y tendencias |

El plugin funciona sin ellos — recurre automáticamente a WebSearch.

---

## Licencia

MIT

---

<div align="center">

**Entra una frase. Salen cuatro documentos.**

</div>
