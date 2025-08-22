---
id: pocket_selenium_chromedriver
title: "Selenium/ChromeDriver — fix rápidos"
slug: /pocket/selenium_chromedriver
module: apendice-pocket
version: 0.1.0
status: draft
owners: [atlas-core]
tags: [selenium, chromedriver, webdriver-manager, troubleshooting]
source_repo: <repo-url>
source_path: docs/pocket/selenium_chromedriver.md
---

## Síntoma típico
`SessionNotCreatedException`: el **ChromeDriver** no coincide con la versión de **Chrome** instalada; o inicializás mal `options/service`. <!-- removed contentReference -->

## Receta 1 — Alinear versiones automáticamente
~~~bash
pip install -U selenium webdriver-manager
~~~

~~~python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager

opts = Options()
# opcional:
# opts.add_argument("--headless=new")

driver = webdriver.Chrome(
    service=Service(ChromeDriverManager().install()),
    options=opts
)
~~~

* **Evitar** pasar `options` dos veces / usar `chrome_options` (deprecado).
* Si falla `webdriver-manager`, bajá el binario y pasa `Service('/path/chromedriver')`.&#x20;

## Receta 2 — Cuando el manager da error

1. `pip install --upgrade webdriver-manager selenium`
2. Verificar compatibilidades (Python/selenium/manager)
3. Usar `Options()` correctamente (no strings).&#x20;

## Notas de entorno

* En **headless/CI**, usa flags (`--headless`, `--no-sandbox`, etc.) según tu runner.
* Si corrés en WSL/containers sin GUI, preferí headless.&#x20;

