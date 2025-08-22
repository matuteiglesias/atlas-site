---
id: operacion_ci_github_actions
title: "CI/CD — GitHub Actions (tokens, secrets, dispatch, runbook 401/403)"
slug: /operacion/ci_github_actions
module: operacion
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [ops, github, actions, tokens, secrets, dispatch, 403, 401]
source_repo: <repo-url>
source_path: docs/operacion/ci_github_actions.md
---

## 1) Tokens y permisos
- **GITHUB_TOKEN** vs **PAT**: cuándo usar cada uno; scopes requeridos `repo` + `workflow`. 
- **Header**: `Authorization: token ...` (o `Bearer` si tu política lo exige). <!-- removed contentReference -->

## 2) Secrets
- **Secret name** ≠ valor del token; cómo referenciarlo en YAML (`secrets.MI_PAT`). 
- Gestión/listado de nombres de secrets con `gh secret list`. <!-- removed contentReference -->

## 3) Triggers entre repos
- Preferencia: `repository_dispatch` si **mismo owner**; alternativa `workflow_dispatch` con `dispatches`. Reglas de ownership/permisos. 

## 4) Runbook de errores (401/403)
- **401 Bad credentials**: nombre del secret, scopes, token expirado. 
- **403 Resource not accessible / Forbidden**: owner distinto, branch protection, políticas org. 
- Paso a paso con `curl` y Octokit para aislar causa. <!-- removed contentReference -->

## Contrato mínimo
- **Secrets**: `GH_AUTH_TOKEN` (PAT con `repo,workflow`).
- **Workflow**: job “notify-atlas” con POST `dispatches` + payload `event_type`.
- **Verificación**: Actions de origen y destino marcan *green* y evento recibido. <!-- removed contentReference -->
