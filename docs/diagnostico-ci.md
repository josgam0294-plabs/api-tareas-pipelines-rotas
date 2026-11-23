# Laboratorio de integración continua

## 1. Enlaces

| Qué | Enlace |
|---|---|
| Repositorio en el tag `lab-ci` | https://github.com/josgam0294-plabs/api-tareas-pipelines-rotas/tree/lab-ci |
| Pull request que el check bloqueó (prueba rota a propósito) | https://github.com/josgam0294-plabs/api-tareas-pipelines-rotas/actions/runs/45084865141|
| Pull request que el check bloqueó (prueba rota a propósito) | https://github.com/josgam0294-plabs/api-tareas-pipelines-rotas/actions/runs/45087586039 |
| Pull request que el check bloqueó (prueba rota a propósito) | https://github.com/josgam0294-plabs/api-tareas-pipelines-rotas/actions/runs/45089119522 |

## 2. Diagnóstico de los tres pipelines rotos

### Caso 1

**Síntoma** (el paso que falló primero y su mensaje, copiado del log como texto):

```text
<failure message="AssertionError: assert 400 == 201 + where 400 = <WrapperTestResponse streamed [400 BAD REQUEST]>.status_code + where <WrapperTestResponse streamed [400 BAD REQUEST]> = post('/tareas', json={'titulo': 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'}) + where post = <FlaskClient <Flask 'app'>>.post">cliente = <FlaskClient <Flask 'app'>> def test_titulo_en_el_limite_se_acepta(cliente): > assert cliente.post("/tareas", json={"titulo": "x" * 120}).status_code == 201 E AssertionError: assert 400 == 201 E + where 400 = <WrapperTestResponse streamed [400 BAD REQUEST]>.status_code E + where <WrapperTestResponse streamed [400 BAD REQUEST]> = post('/tareas', json={'titulo': 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'}) E + where post = <FlaskClient <Flask 'app'>>.post tests/test_api.py:79: AssertionError</failure>
```

**Causa:** La configuración de ci.yml apuntaba al archivo regular de requirements.txt por lo cual le faltaban los paquetes de testeo al ambiente


**Corrección:** Se apunta la configuración en ci.yml al archivo correcto de requirements, requirements-dev.txt · Commit: [enlace al commit de la corrección](https://github.com/josgam0294-plabs/api-tareas-pipelines-rotas/pull/5/changes/2d039bbc957f821ec241b2bf019e7c5cd124610d)

**Prevención:** El ruleset permite confirmar que todas las pruebas requeridas sean ejecutadas previo a incluirlo en main.

### Caso 2

**Síntoma** (el paso que falló primero y su mensaje, copiado del log como texto):

```text
Error: No files were found with the provided path: reporte/. No artifacts will be uploaded.
```

**Causa:** En el archivo de ci.yml el folder donde "caen" las pruebas no es el correcto

**Corrección:** se corrige el nombre del folder de "reporte" a "reportes"· Commit: [enlace al commit de la corrección](https://github.com/josgam0294-plabs/api-tareas-pipelines-rotas/pull/6/changes/df5eec5cd7b25d2a297e80b074622bc0cf8b39a4)

**Prevención:** Igualmente el ruleset requiere que la configuración del pipeline se la apropiada para poder validar que las pruebas fueron ejecutadas antes de incluir el branch a main.

### Caso 3

**Síntoma** (el paso que falló primero y su mensaje, copiado del log como texto):

```text
<error message="failed on setup with "FileNotFoundError: [Errno 2] No such file or directory: '/home/runner/work/api-tareas-pipelines-rotas/api-tareas-pipelines-rotas/tests/datos/tareas_ejemplo.json'"">
@pytest.fixture def tareas_ejemplo(): > return json.loads(DATOS.read_text(encoding="utf-8")) ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ tests/test_datos_ejemplo.py:14: _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ /opt/hostedtoolcache/Python/3.12.16/x64/lib/python3.12/pathlib.py:1027: in read_text with self.open(mode='r', encoding=encoding, errors=errors) as f: ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ self = PosixPath('/home/runner/work/api-tareas-pipelines-rotas/api-tareas-pipelines-rotas/tests/datos/tareas_ejemplo.json') mode = 'r', buffering = -1, encoding = 'utf-8', errors = None, newline = None def open(self, mode='r', buffering=-1, encoding=None, errors=None, newline=None): """ Open the file pointed to by this path and return a file object, as the built-in open() function does. """ if "b" not in mode: encoding = io.text_encoding(encoding) > return io.open(self, mode, buffering, encoding, errors, newline) ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ E FileNotFoundError: [Errno 2] No such file or directory: '/home/runner/work/api-tareas-pipelines-rotas/api-tareas-pipelines-rotas/tests/datos/tareas_ejemplo.json' /opt/hostedtoolcache/Python/3.12.16/x64/lib/python3.12/pathlib.py:1013: FileNotFoundError
</error>
```

**Causa:** Un archivo JSON de pruebas es eliminado con el archivo de .gitignore, por lo cual la prueba no se puede completar apropiadamente

**Corrección:** se comenta la línea que filtra los archivos JSON en el archivo .gitignore · Commit: [enlace al commit de la corrección](https://github.com/josgam0294-plabs/api-tareas-pipelines-rotas/pull/7/changes/7abb431b9a1fde8c1c6c603d0e084b87cbfa6eb1)

**Prevención:** La configuración del pipeline asegura que nuevas pruebas agregadas a los paquetes de testeo sean ejecutadas previamente antes de agregar a main.

## 3. Uso de IA

"No se usó IA"
