---
title: "Inicio — Atlas de Pobreza"
sidebar_position: 1
slug: /
version: 0.1.0
status: draft
owners: [atlas-core]
source_repo: <repo-url>
source_path: index.md
---

# Atlas de Pobreza

Este sitio organiza, documenta y estandariza los métodos, herramientas y prácticas que usamos para construir el **Atlas de Pobreza** — un esfuerzo modular, reproducible y crítico sobre datos socioeconómicos en el Sur Global.

Es un recurso operativo para equipos técnicos, investigadores y actores institucionales. No es un paper. No es un blog. Es una mezcla de manual, playbook y referencia para construir sistemas que midan pobreza y desigualdad con criterios explícitos, versiones claras y práctica reproducible.

---

## 📁 Estructura del sitio

El Atlas está organizado en secciones temáticas. Cada una tiene su índice y secciones internas.

### 🧪 [Métodos](/docs/category/métodos)
Bloques modulares y herramientas para el procesamiento, análisis y visualización de datos. Incluye:
- Charts y estilos
- Toolkits para notebooks, modelado y ETL
- Integraciones geográficas
- Temporalidad y estructuras modulares

### ⚙️ [Operación](/docs/category/operación)
Guías sobre infraestructura operativa, versionado, naming, logs y CI/CD. Apunta a prácticas sostenibles y reproducibles.

### 🧾 [Referencia](/docs/category/referencia)
Documentación de las fuentes base: censos, geometrías, variables núcleo. No describe procesos, sino los elementos de entrada.

### 📘 [Catálogo](/docs/category/catálogo)
Indicadores, outputs y artefactos públicos. Por ahora, documentos de población sintética y métricas de pobreza.

### 🔧 [Pocket](/docs/category/pocket)
Tips rápidos, patrones de código, micro-recetas y checklists. Pensado para resolver problemas operativos concretos. Por ejemplo:
- Cómo unir geometrías y CSVs con Mapbox
- Cheatsheets para logs, timestamps, merge y estilo de gráficos
- Recetas para evitar errores comunes con JSON, CLI y Git

### 📚 [Playbooks](/docs/playbooks)
**Workflows completos**, aún en desarrollo. Los playbooks siguen una plantilla tipo:
→ _Inputs → Pasos → QA → Output → Idempotencia_  
Están linkeados a métodos y referencia. Por ahora, el índice resume lo planificado.

---

## 🔎 Cómo buscar

- Usá la barra de búsqueda global (arriba) para encontrar por palabra clave.
- Las etiquetas (tags) permiten filtrar contenidos por temas cruzados: `geo`, `etl`, `QA`, `json`, `ops`, etc.

---

## 🚧 Qué está en desarrollo

El sitio está en versión inicial. Algunas páginas están en borrador, otras aún no existen pero están indexadas. Usá el contenido existente como base; los métodos y el pocket ya están listos para uso operativo.

Podés seguir el avance en la sección de [Playbooks](/playbooks) o directamente en el [repositorio fuente](<repo-url>).

---

> _Medir bien no alcanza. Pero medir mal lo arruina todo._

