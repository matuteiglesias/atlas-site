---
id: pocket_git_large_files
title: "Git — archivos grandes (BFG/LFS)"
slug: /pocket/git_large_files
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [git, large-files, bfg, lfs, history, troubleshooting]
source_repo: <repo-url>
source_path: docs/pocket/git_large_files.md
---

## Problema
GitHub bloquea el push por archivos >100 MB en **historia** (aunque hoy ya no estén en `HEAD`). Necesitás **verificar** si siguen referenciados y **purgarlos** correctamente. <!-- removed contentReference -->

## Verificaciones rápidas
~~~bash
# 1) ¿Qué se va a empujar?
git diff --stat origin/main main

# 2) ¿Sigue en la historia?
git log -- path/grande.csv

# 3) Objetos y tamaños del commit actual
git ls-tree -r -l HEAD | sort -k4 -n | tail

# 4) Objetos huérfanos (post-limpieza)
git fsck --dangling

# 5) Tamaño del repositorio (orientativo)
du -sh .git/
~~~

Si aparece en (1) o (2), **sigue en el diferencial** que vas a empujar. (3–5) ayudan a confirmar limpieza y a diagnosticar bloat.&#x20;

## Remedios (elige según impacto)

**A. Reescribir commits recientes (squash / interactive rebase)**

* Compactá los commits donde se agregó y luego se borró.
* *Luego* remové los pesados del staging y **force push**.
* Avisar al equipo (todos deberán re-clone/reset).&#x20;

**B. Purgado histórico (BFG o filter-branch)**

* **BFG Repo-Cleaner** (preferido por simplicidad) o `git filter-branch`.
* Reescribe historia, requiere **`--force`** al pushear.
* Post-limpieza: `git gc` y verificación con `git fsck --dangling`.&#x20;

**C. Si están untracked pero molestan**

* Eliminá los archivos del working dir para evitar commits accidentales.
* Considerá `.gitignore` o **Git LFS** si debés versionarlos.&#x20;

## Contrato mínimo (política)

* **No** versionar binarios >50 MB en repos de código.
* Artefactos → releases/almacenamiento externo; o **Git LFS** si es imprescindible.
* Toda reescritura de historia: anuncio + plan de re‐sync para el equipo.&#x20;
