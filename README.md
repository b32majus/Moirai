# Moirai

**Moirai** es un sistema personal de vestuario asistido por IA cuyo objetivo no es “seguir la moda”, sino reducir la fricción cotidiana de decidir qué ponerse, aprovechar mejor la ropa existente, comprar con más criterio y adaptar el vestuario al contexto real de uso.

El proyecto nace de una necesidad concreta: externalizar una tarea de decisión que hoy requiere demasiado esfuerzo y produce repetición, infrautilización del armario, compras poco integradas y dificultad para ajustar la ropa a trabajo, viajes, eventos y ocio.

## Estado actual

**Discovery / reuse-first.**

No se ha decidido construir una aplicación propia. La hipótesis actual es reutilizar al máximo software open source existente y añadir sólo las capacidades diferenciales que falten.

La primera candidata a auditar como base es **Wardrowbe**, con otras referencias como **AI Closet** y **Libre Closet**. La arquitectura preliminar separa:

- una capa visual / inventario para gestionar prendas, fotos, outfits e historial;
- una capa de agente o “estilista” para el razonamiento contextual, auditoría del armario, viajes y compras.

## Principio rector

> No construir la NASA antes de comprobar que necesitamos un avión.

Primero validaremos el problema, el estilo objetivo y la utilidad real de las recomendaciones con un armario piloto. Sólo después decidiremos qué reutilizar, modificar o construir.

## Documentación

- [`docs/START_HERE.md`](docs/START_HERE.md) — punto de entrada y autoridad actual.
- [`docs/PRODUCT_VISION_V0.md`](docs/PRODUCT_VISION_V0.md) — problema, necesidades y comportamiento esperado.
- [`docs/RESEARCH_BASELINE_V0.md`](docs/RESEARCH_BASELINE_V0.md) — soluciones y componentes ya identificados.
- [`docs/DECISIONS_V0.md`](docs/DECISIONS_V0.md) — decisiones iniciales y límites.
- [`docs/EXECUTION_PLAN_V0.md`](docs/EXECUTION_PLAN_V0.md) — fases y siguiente trabajo.

## Nombre

El nombre **Moirai** hace referencia a las Moiras y al “hilo” como metáfora del sistema. Si en el futuro resulta útil, sus tres figuras pueden inspirar responsabilidades internas:

- **Clotho** — incorporar e inventariar prendas.
- **Lachesis** — combinar, planificar y distribuir el vestuario.
- **Atropos** — retirar, sustituir o cerrar el ciclo de una prenda.

Estos nombres son semántica de producto provisional, no una arquitectura obligatoria.
