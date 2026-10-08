# SYNC-GITHUB — SINCRONIZACIÓN DE LA MEMORIA CON EL REPO DEL USUARIO

- Versión: 1.1
- Creado: 2026-10-08 (a pedido del usuario)
- v1.1: REPO por variable de entorno GITHUB_REPO (una sola fuente de configuración) y
  sección de PUESTA EN MARCHA para el agente del usuario.
- Para quién: la IA que opere esta memoria en CUALQUIER sesión (incluida la sesión de la
  usuaria en su propia cuenta de Z.ai).

---

## LA IDEA EN UNA FRASE

La memoria vive en el repo **privado de GitHub del usuario (el hijo)**. Cada sesión de
trabajo la mantiene sincronizada: **pull al inicio** (bajar mejoras que el usuario hizo en
el repo) y **push al cierre** (subir lo aprendido en la sesión). Así el usuario puede
analizar cómo trabaja la usuaria, mejorar los archivos y que las mejoras vuelvan a ella
en la próxima sesión, sin que la usuaria toque nada técnico.

```
Sesión de ella (su cuenta Z.ai)          Repo privado de GitHub            Usuario (el hijo)
├─ 1. Carga el ZIP + lee memoria   ──►   (pull: baja mejoras)   ◄──   edita/mejora los .md
├─ 2. Trabaja (contrato, revisor)
└─ 3. Ritual de cierre: actualiza  ──►   (push: sube cambios)   ──►   analiza diffs y patrones
   memoria y hace push
```

---

## REGLAS DE SEGURIDAD DEL TOKEN (obligatorias)

1. El token vive SOLO en dos lugares: la línea `TOKEN` de la sección CONFIGURACIÓN de
   este archivo, y la variable de entorno del comando que ejecuta el agente.
2. **NUNCA imprimir el token**: ni en respuestas, ni en DIARIO.md, ni en informes, ni en
   logs, ni en mensajes de error. Si un error muestra el token, repite el paso sin mostrarlo.
3. El token solo tiene permisos sobre ESTE repo (fine-grained, Contents: Read and write).
   No sirve para nada más — por eso puede viajar dentro de la memoria.
4. **Solo se suben/bajan los .md de esta memoria.** NUNCA subir Excels, datos de la
   usuaria, cédulas, cuentas ni nombres (regla 11 de REGLAS.md). El repo es de memoria,
   no de datos.
5. Si el push falla con 401/403 → el token expiró o fue revocado: informarlo al usuario
   con esa frase exacta y NO reintentar en bucle. Trabajar sin push y anotarlo en el diario.
6. Si no hay internet, el push/pull falla: continuar la sesión normal y anotarlo en el
   diario como "pendiente de sincronizar".

---

## CUÁNDO SE EJECUTA

- **Pull (bajar) — al inicio de la sesión**, justo después de leer la memoria y antes del
  primer mensaje de trabajo: si el usuario mejoró los archivos en el repo, aquí llegan.
  Si el pull trae archivos cambiados, recargarlos y avisar en 1 línea
  ("Memoria actualizada desde el repo: X archivos nuevos").
- **Push (subir) — al cierre de la sesión**, como paso del ritual (ver PROTOCOLO.md),
  después de escribir las actualizaciones de memoria y regenerar el ZIP si aplica.
- Mensaje del commit: corto y útil para el diff, ej.:
  `Sesión 2026-10-08: consolidado octubre, +2 lecciones (L-016/L-017), diario 7 entradas`

---

## CÓMO SE EJECUTA (para el agente, paso a paso)

1. Lee el TOKEN y el REPO de la sección CONFIGURACIÓN de abajo y úsalos como variables
   de entorno `GITHUB_TOKEN` y `GITHUB_REPO`.
2. Escribe el script de la sección siguiente en un archivo temporal
   (ej. `/tmp/sync_github.py`) tal cual está, SIN modificarlo.
3. Ejecuta (la carpeta es donde quedó extraída esta memoria):

```bash
# Subir cambios (al cierre):
GITHUB_TOKEN="VALOR_TOKEN" GITHUB_REPO="valor/de-repo" python3 /tmp/sync_github.py /ruta/a/memoria-portatil push "mensaje del commit"

# Bajar mejoras (al inicio):
GITHUB_TOKEN="VALOR_TOKEN" GITHUB_REPO="valor/de-repo" python3 /tmp/sync_github.py /ruta/a/memoria-portatil pull
```

4. Reporta al usuario en 1-2 líneas: qué se subió/bajó y el link del commit (el push lo
   imprime). Nunca el token.
5. Borra el archivo temporal con el script si el entorno lo permite (opcional, higiene).

---

## CONFIGURACIÓN (el usuario completa estos dos valores)

```
REPO = "Jg-Stevan/memoria-tesoreria"
TOKEN = "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"
```

Estos dos valores son los que se pasan como `GITHUB_REPO` y `GITHUB_TOKEN` en el comando.
El script NO guarda el repo en su código: lo lee del entorno cada vez (una sola fuente
configurable, y rotar el token no toca el script).

---

## PUESTA EN MARCHA (una sola vez — el usuario la hace con su agente de confianza)

### Parte A — pasos manuales del usuario (2 minutos, en el navegador)

1. **Crear el repo privado**: `github.com/new` → nombre (ej. `memoria-tesoreria`) →
   **Private** → Create. Vacío, sin README (el script crea la rama sola).
2. **Crear el token fine-grained**: GitHub → foto → **Settings** → **Developer settings** →
   **Personal access tokens** → **Fine-grained tokens** → **Generate new token**:
   - Name: `memoria-tesoreria-sync` · Expiration: 90 días
   - Repository access: **Only select repositories** → SOLO `memoria-tesoreria`
   - Permissions → Repository permissions → **Contents: Read and write**
     (Metadata se queda en Read-only, es automático)
   - Todo lo demás en **No access** → **Generate token** → copiar el `github_pat_...`
     (se muestra UNA sola vez).
3. Ten a mano los dos valores: el nombre del repo (`usuario/memoria-tesoreria`) y el token.

### Parte B — instrucción para el agente (pega esto en un chat con ejecución de código,
junto con el ZIP de la memoria subido como archivo)

```
AGENTE: configura y prueba la sincronización de mi memoria portátil con GitHub.

Te doy yo los dos valores:
- NOMBRE_REPO = "usuario/memoria-tesoreria"  (REEMPLAZAR por el mío real)
- TOKEN = "github_pat_XXXX"                  (REEMPLAZAR por el mío real)

La memoria está en memoria-portatil.zip que te subí. Haz esto EN ORDEN:

1. Extrae el ZIP en una carpeta de trabajo y verifica que exista SYNC-GITHUB.md.
2. Edita la sección CONFIGURACIÓN de SYNC-GITHUB.md y deja los valores reales:
   REPO = "NOMBRE_REPO" y TOKEN = "TOKEN" (sin corchetes ni [POR CONFIRMAR]).
3. Regenera memoria-portatil.zip con TODOS los .md de la carpeta (ya configurados).
   Ese ZIP configurado es el que se le entrega a la usuaria.
4. Escribe el script de la sección "EL SCRIPT" de SYNC-GITHUB.md en /tmp/sync_github.py
   tal cual está, SIN modificarlo.
5. PRUEBA DE PUSH (token SOLO en variables de entorno, jamás impreso en pantalla):
   GITHUB_TOKEN="TOKEN" GITHUB_REPO="NOMBRE_REPO" python3 /tmp/sync_github.py CARPETA push "Prueba de configuracion - memoria v1.3"
   (CARPETA = la ruta donde extrajiste la memoria)
6. PRUEBA DE PULL (debe responder "ya estaba igual"):
   GITHUB_TOKEN="TOKEN" GITHUB_REPO="NOMBRE_REPO" python3 /tmp/sync_github.py CARPETA pull
7. Borra /tmp/sync_github.py al terminar. Reporta: el link del commit, cuántos archivos
   se subieron, y el ZIP configurado listo para descargar.

Si el push falla, di la causa exacta y ESPERA (no reintentes en bucle):
- 401 = token inválido o expirado → genero otro.
- 404 = el repo no existe, o el token no incluye ese repo (revisar "Only select repositories").
- 403 = al token le falta Contents: Read and write.

REGLAS INNEGOCIABLES: nunca imprimas el token en la respuesta ni en logs; nunca lo
escribas en ningún archivo distinto de SYNC-GITHUB.md; nunca subas al repo algo distinto
de los .md de la memoria (jamás Excel ni datos de la usuaria).
```

### Parte C — después de la puesta en marcha

- El ZIP configurado (con el token dentro de SYNC-GITHUB.md) es el que se le da a la
  usuaria para sus sesiones — ella nunca ve ni toca el token.
- Pon un recordatorio de calendario a los 85 días: el token expira a los 90. Rotarlo es
  repetir la Parte A paso 2 y actualizar la sección CONFIGURACIÓN.
- Si algo se ve raro en el repo (commits que no reconoces), revoca el token al instante
  desde Settings → Fine-grained tokens y genera otro.

---

## EL SCRIPT (portable, solo librerías estándar de Python)

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# sync_github.py — Sincroniza la memoria portátil con el repo privado de GitHub.
# Uso: GITHUB_TOKEN=xxx python3 sync_github.py /ruta/memoria-portatil push "mensaje"
#      GITHUB_TOKEN=xxx python3 sync_github.py /ruta/memoria-portatil pull
# El token NUNCA se imprime. Solo se suben/bajan los .md de la memoria.
import base64, hashlib, json, os, sys, urllib.request, urllib.error
from pathlib import Path

REPO = os.environ.get("GITHUB_REPO", "").strip()   # valor de REPO en la seccion CONFIGURACION
BRANCH = "main"
API = "https://api.github.com"

def api(metodo, ruta, cuerpo=None):
    req = urllib.request.Request(
        API + ruta,
        data=json.dumps(cuerpo).encode() if cuerpo is not None else None,
        method=metodo,
        headers={
            "Authorization": "Bearer " + os.environ.get("GITHUB_TOKEN", ""),
            "Accept": "application/vnd.github+json",
            "User-Agent": "memoria-portatil-sync",
            "Content-Type": "application/json",
        },
    )
    try:
        with urllib.request.urlopen(req) as r:
            datos = r.read()
            return r.status, (json.loads(datos) if datos else {})
    except urllib.error.HTTPError as e:
        return e.code, e.read().decode(errors="replace")[:300]

def sha_blob(datos: bytes) -> str:
    return hashlib.sha1(b"blob %d\0" % len(datos) + datos).hexdigest()

def push(carpeta, mensaje):
    archivos = sorted(Path(carpeta).glob("*.md"))
    if not archivos:
        raise SystemExit("No hay .md en " + carpeta)
    cod, ref = api("GET", "/repos/%s/git/ref/heads/%s" % (REPO, BRANCH))
    base = ref["object"]["sha"] if cod == 200 else None
    arbol_base = None
    remotos = {}
    if base:
        cod, cb = api("GET", "/repos/%s/git/commits/%s" % (REPO, base))
        if cod == 200:
            arbol_base = cb["tree"]["sha"]
            cod, arbol = api("GET", "/repos/%s/git/trees/%s?recursive=1" % (REPO, arbol_base))
            if cod == 200:
                remotos = {t["path"]: t["sha"] for t in arbol.get("tree", []) if t.get("type") == "blob"}
    nuevos = []
    for a in archivos:
        datos = a.read_bytes()
        if remotos.get(a.name) == sha_blob(datos):
            continue  # ya está igual en el repo
        cod, blob = api("POST", "/repos/%s/git/blobs" % REPO,
                        {"content": base64.b64encode(datos).decode(), "encoding": "base64"})
        if cod != 201:
            raise SystemExit("No se pudo crear el blob de %s: %s" % (a.name, blob))
        nuevos.append({"path": a.name, "mode": "100644", "type": "blob", "sha": blob["sha"]})
    if not nuevos:
        print("Sin cambios respecto al repo. Nada que subir.")
        return
    cuerpo = {"tree": nuevos}
    if arbol_base:
        cuerpo["base_tree"] = arbol_base
    cod, arbol = api("POST", "/repos/%s/git/trees" % REPO, cuerpo)
    if cod != 201:
        raise SystemExit("Arbol: %s" % arbol)
    cod, commit = api("POST", "/repos/%s/git/commits" % REPO,
                      {"message": mensaje, "tree": arbol["sha"], "parents": [base] if base else []})
    if cod != 201:
        raise SystemExit("Commit: %s" % commit)
    if base:
        cod, _ = api("PATCH", "/repos/%s/git/refs/heads/%s" % (REPO, BRANCH), {"sha": commit["sha"]})
        if cod != 200:
            raise SystemExit("No se pudo avanzar la rama (¿otro push simultaneo?). Reintenta.")
    else:
        cod, _ = api("POST", "/repos/%s/git/refs" % REPO,
                     {"ref": "refs/heads/%s" % BRANCH, "sha": commit["sha"]})
        if cod != 201:
            raise SystemExit("No se pudo crear la rama: %s" % _)
    print("OK: %d archivo(s) subidos -> commit %s" % (len(nuevos), commit["sha"][:7]))
    print("https://github.com/%s/commit/%s" % (REPO, commit["sha"]))

def pull(carpeta):
    cod, ref = api("GET", "/repos/%s/git/ref/heads/%s" % (REPO, BRANCH))
    if cod != 200:
        print("El repo aun no tiene rama main (nada que bajar).")
        return
    cod, cb = api("GET", "/repos/%s/git/commits/%s" % (REPO, ref["object"]["sha"]))
    cod, arbol = api("GET", "/repos/%s/git/trees/%s?recursive=1" % (REPO, cb["tree"]["sha"]))
    bajados = []
    for t in arbol.get("tree", []):
        if t.get("type") == "blob" and t["path"].endswith(".md") and "/" not in t["path"]:
            cod, blob = api("GET", "/repos/%s/git/blobs/%s" % (REPO, t["sha"]))
            if cod != 200:
                raise SystemExit("No se pudo bajar %s" % t["path"])
            contenido = base64.b64decode(blob["content"])
            destino = Path(carpeta) / t["path"]
            if destino.exists() and destino.read_bytes() == contenido:
                continue
            destino.write_bytes(contenido)
            bajados.append(t["path"])
    print("OK: %d archivo(s) actualizados desde el repo%s" %
          (len(bajados), (": " + ", ".join(bajados)) if bajados else " (ya estaba igual)"))

def main():
    if not REPO:
        raise SystemExit("Falta GITHUB_REPO (lee el valor de REPO en la seccion CONFIGURACION de SYNC-GITHUB.md).")
    if not os.environ.get("GITHUB_TOKEN"):
        raise SystemExit("Falta la variable de entorno GITHUB_TOKEN.")
    if len(sys.argv) < 3:
        raise SystemExit("Uso: python3 sync_github.py /ruta/memoria-portatil push|pull [mensaje]")
    carpeta, accion = sys.argv[1], sys.argv[2]
    if accion == "push":
        push(carpeta, sys.argv[3] if len(sys.argv) > 3 else "Actualizacion de memoria")
    elif accion == "pull":
        pull(carpeta)
    else:
        raise SystemExit("Accion desconocida: %s (usa push o pull)" % accion)

if __name__ == "__main__":
    main()
```

---

## PROTOCOLO DE ESTE ARCHIVO

- Si el usuario cambia el repo o rota el token, actualizar SOLO la sección CONFIGURACIÓN.
- Si la práctica muestra que falta un caso (conflictos de push simultáneo, ramas extra),
  agregarlo aquí y en LECCIONES.md.
- Este archivo es infraestructura del sistema: NO contiene datos de la usuaria y puede
  viajar dentro del ZIP sin riesgo (el token solo vale para este repo).
