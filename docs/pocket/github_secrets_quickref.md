---
id: pocket_github_secrets_quickref
title: "GitHub Secrets — quick-ref"
slug: /pocket/github_secrets_quickref
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [github, actions, secrets, pat, ci]
source_repo: <repo-url>
source_path: docs/pocket/github_secrets_quickref.md
---

## Propósito
Recordatorio mínimo para **listar/usar** *secrets* y resolver el clásico **“Bad credentials”**.

## Hechos clave
- Los **valores** de los secrets **no se pueden ver** una vez seteados (solo los **nombres**). Gestión desde *Settings → Secrets and variables → Actions*, o por CLI. <!-- removed contentReference -->

## Listar nombres de secrets

### Vía GitHub CLI
~~~bash
brew install gh     # macOS
sudo apt install gh # Linux
gh auth login
gh secret list
~~~

Muestra **nombres** (no valores).&#x20;

### Desde un workflow (solo nombres)

~~~yaml
name: List Secrets
on: [push]
jobs:
  list-secrets:
    runs-on: ubuntu-latest
    steps:
      - name: Display secret names
        run: |
          echo "Available secrets:"
          echo ${{ toJSON(secrets) }} | jq 'keys[]'
~~~

Imprime las **keys** disponibles. Útil para auditar naming.&#x20;

## Uso correcto en YAML (dispatch a otro repo)

~~~yaml
- name: Send repository dispatch event
  run: |
    curl -X POST \
      -H "Accept: application/vnd.github.v3+json" \
      -H "Authorization: token ${{ secrets.gh-auth-token }}" \
      https://api.github.com/repos/<owner>/<repo>/dispatches \
      -d '{"event_type":"models-updated"}'
~~~

Asegurate de referenciar el **nombre real** del secret (no placeholders).&#x20;

## “Bad credentials” — mini-runbook

1. **Nombre del secret** en YAML coincide con Settings.&#x20;
2. **Scopes del PAT**: `repo`, `workflow`.&#x20;
3. **Regenerar PAT** si hay dudas y volver a guardarlo como secret.&#x20;
4. **Espacios extra** al copiar/pegar → eliminar.&#x20;
5. **URL del repo destino** exacta.&#x20;

> Si el problema persiste, migrar a Octokit para mejor manejo de errores o usar `workflow_dispatch` según ownership/permisos.&#x20;

## QA mínimo

* Job de prueba que liste **solo nombres** (sin exponer valores).&#x20;
* Acción de `dispatch` deja *green* en origen y evento recibido en destino (logs de ambos).

