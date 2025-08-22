---
id: operacion_git_archivos_grandes
title: "Git — manejo de archivos grandes e historia"
slug: /operacion/git_archivos_grandes
module: operacion
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [ops, git, lfs, bfg, historia]
source_repo: <repo-url>
source_path: docs/operacion/git_archivos_grandes.md
---

## Problema
Push bloqueado por archivos >100MB presentes en **historia**, aunque no estén en HEAD. <!-- removed contentReference -->

## Diagnóstico
- `git log -- <archivo>` y `git diff --stat origin/main main`.  
- Tamaños con `git ls-tree -r -l HEAD` y chequeo `.git/` (volumen). <!-- removed contentReference -->

## Remedios
- **Reescritura de historia**: BFG o `filter-branch` (cautela y re‐clone).  
- **Squash/interactive rebase** si los añadidos/remociones son recientes.  
- **Git LFS** para binarios legítimos en repos activos. <!-- removed contentReference -->

## Contrato mínimo
Política de inclusión: binarios externos → artefactos release o storage; repos limpios de >50MB.
