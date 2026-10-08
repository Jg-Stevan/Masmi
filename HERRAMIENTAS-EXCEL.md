# HERRAMIENTAS DEL ENTORNO — EXCEL DE INICIO A FIN

- Versión: 1.2
- Última actualización: 2026-10-08
- Para quién: la IA que opere en cualquier chat. Este archivo es el manual técnico para
  cumplir el contrato de trabajo con la usuaria.
- v1.1: mejoras derivadas de la primera prueba real completa (consolidado 2026, 2026-10-08).
- v1.2: la entrega pasa por la revisión independiente de AGENTE-REVISOR.md (a pedido del usuario).

---

## EL CONTRATO DE TRABAJO (definido por el usuario, 2026-10-08)

El flujo con la usuaria es siempre este:

```
1. Ella sube un archivo Excel
2. Ella pide algo en sus palabras ("hazme el cruce", "adiciona las columnas", "limpia octubre")
3. El asistente trabaja sobre una COPIA del archivo
4. El asistente verifica su trabajo (Protocolo 5) y lo pasa por la revisión
   independiente (AGENTE-REVISOR.md) — solo se entrega lo aprobado
5. El asistente DEVUELVE EL ARCHIVO EN EL MISMO FORMATO con la tarea realizada
6. Y narra en español simple qué hizo y qué encontró
```

**El formato entra y sale igual.** Mismo tipo de archivo (.xlsx), misma estructura de hojas,
mismos colores, mismas fórmulas, mismo estilo. Lo único que cambia es la tarea que ella pidió.
Si por alguna razón el resultado no puede ser un xlsx idéntico en formato, se explica ANTES,
no después.

---

## EL ENTORNO DE TRABAJO (verificado en sandbox Z.ai, 2026-10-08)

Herramientas confirmadas disponibles en el entorno de ejecución de código:

| Herramienta | Estado | Para qué se usa |
|---|---|---|
| Python 3.12 | Disponible | Todo el procesamiento |
| openpyxl 3.1.5 | Disponible | Leer/escribir xlsx con estilos, colores y fórmulas |
| pandas 2.2.3 | Disponible | Cruces, pivotes, análisis de datos en volumen |
| LibreOffice (soffice) | Disponible | Verificación visual (convertir a PDF) y validación de archivos |
| lxml / zipfile / hashlib / shutil | Disponibles | Cirugía XML del xlsx, copias seguras, huellas |

**Regla del entorno**: al empezar en un chat nuevo, correr primero el diagnóstico
(sección siguiente) y NO asumir versiones; si algo falta, adaptar la técnica y decirlo.

### Diagnóstico de arranque (copiar y ejecutar en cualquier chat nuevo)

```python
import sys, zipfile
print("Python:", sys.version.split()[0])
try:
    import openpyxl; print("openpyxl:", openpyxl.__version__)
except ImportError: print("openpyxl: NO DISPONIBLE")
try:
    import pandas; print("pandas:", pandas.__version__)
except ImportError: print("pandas: NO DISPONIBLE")
import shutil
print("libreoffice:", "sí" if shutil.which("soffice") or shutil.which("libreoffice") else "no")
print("zipfile OK (siempre presente en Python)")
```

---

## PROTOCOLO 1 — RECEPCIÓN Y FINGERPRINT (antes de tocar nada)

Cuando ella suba un archivo:

0. **Verificar que el archivo que menciona la tarea exista.** Si la tarea habla de un
   archivo que no viene en el paquete (ej.: el consolidado "00" no venía entre los meses
   01-09), decirlo y proponer construirlo a partir de lo que sí hay. No asumir que falta
   información: a veces el archivo SE PUEDE construir.
1. **Copiar, nunca trabajar sobre el original**: `shutil.copy2(original, copia_de_trabajo)`.
2. **Huella del original**: `sha256` del archivo original, registrada. Al final se verifica
   que no cambió (regla de oro 4).
3. **Inventario rápido**: número de hojas, dimensiones, hojas ocultas, peso del archivo.

```python
import hashlib, openpyxl
def huella(ruta):
    h = hashlib.sha256()
    with open(ruta, "rb") as f:
        for bloque in iter(lambda: f.read(1 << 20), b""):
            h.update(bloque)
    return h.hexdigest()

wb = openpyxl.load_workbook(copia, data_only=False)  # data_only=False preserva fórmulas
print([(ws.title, ws.max_row, ws.max_column, ws.sheet_state) for ws in wb.worksheets])
```

**Advertencia de tamaño**: para archivos de cientos de miles de filas, usar `read_only=True`
para INVENTARIAR (lectura por trozos, sin cargar todo en memoria), y abrir en modo normal
solo la hoja que se va a editar. Nunca cargar un archivo de 1M de filas completo en memoria
sin necesidad.

---

## PROTOCOLO 2 — LECTURA PROFUNDA (entender el archivo antes de editarlo)

Antes de cualquier edición, leer y reportar (internamente) esto de cada hoja implicada:

0. **Localizar columnas por ENCABEZADO, nunca por letra fija.** El formato de ella CAMBIA
   de estructura entre meses (probado: en septiembre 2026 "RECAUDO MES TESORERIA" pasó de
   la columna G a la H cuando se añadieron columnas acumuladas). Buscar la columna por su
   texto de encabezado en la fila de títulos (fila 9 en el cuadro mensual) y trabajar con
   esa posición. Regla general: la letra de columna es un detalle del MES, no del formato.
1. **Extraer las cifras de control ANTES de editar**: los totales que el propio archivo
   declara (ej.: fila 10 "INGRESOS" del cuadro mensual). Son el patrón de oro contra el que
   se valida todo lo que se construya después (Protocolo 5).
2. **Encabezados**: fila donde empiezan, texto exacto. (Lección L-003: los encabezados
   parecen "valores pegados" y no lo son.)
3. **Fórmulas**: dónde hay, con qué patrón (`.cell.value` con `data_only=False` empieza con "=").
   Detectar si el libro usa shared formulas. Distinguir: fórmulas que referencian hojas del
   MISMO libro (patrón de subtotal) vs fórmulas que referencian libros EXTERNOS (prefijo
   `[n]Hoja!`, patrón de dato importado).
4. **Valores cacheados**: leer con `data_only=True` para obtener los valores calculados que
   Excel guardó. Si una celda con fórmula no tiene valor cacheado, NO es un dato perdido:
   reportarlo y validarlo contra las cifras de control.
5. **Códigos/llaves por fila**: en cuadros jerárquicos, extraer el código (columna A) de
   cada fila y comparar entre archivos del mismo formato: pueden faltar códigos en meses
   anteriores (conceptos creados después) o sobrar filas de etiqueta. El consolidado se
   construye sobre la estructura del archivo MÁS RECIENTE.
6. **Colores**: leer los `fill` de las celdas. Los colores SON un sistema de información de
   ella (marcas de diferencias/pendientes). Pueden ser rgb (ej.: FFFFFF00 = amarillo) o
   theme (colores de tema de Office). Registrar qué color hay y dónde, y si no se sabe su
   significado, preguntarlo (pendiente del GLOSARIO).
7. **Formato de número**: patrones de moneda, fecha, porcentaje (se copian con el estilo
   celda por celda, ver Protocolo 3).
8. **Celdas combinadas** (`ws.merged_cells`), anchos de columna, altura de filas.
9. **Objetos incrustados**: imágenes, gráficos, objetos OLE/Visio/EMF. Revisar el xlsx como
   ZIP (`zipfile`) y listar `xl/embeddings/`, `xl/charts/`, `xl/drawings/`. Si hay objetos
   que openpyxl podría perder, activar cirugía XML (Protocolo 4).
10. **Muestra narrable**: 5-10 filas de ejemplo para poder describirle a ella qué contiene
   el archivo en su idioma.

---

## PROTOCOLO 3 — EDICIÓN PRESERVANDO FORMATO (la técnica por defecto)

**Orden de preferencia de técnicas** (de menos a más invasiva):

1. **openpyxl sobre la copia** (técnica por defecto): abrir la copia, editar SOLO las celdas
   que la tarea pide, guardar. openpyxl preserva estilos, fórmulas y colores de las celdas
   que no se tocan.
2. **pandas para el análisis, openpyxl para la escritura**: los cruces/sumas/filtros se
   hacen con pandas leyendo los datos, pero el resultado se escribe con openpyxl celda por
   celda en la copia, para no destruir el formato.
3. **Cirugía XML directa** (cuando openpyxl no alcanza): ver Protocolo 4.

**Reglas de escritura**:
- **Estilo por fila desde la columna modelo**: si la tarea reorganiza columnas, copiar el
  estilo de cada fila desde una celda de la columna que representa el dato (ej.: la columna
  de tesorería del mes) hacia las celdas nuevas — así los colores de sección y formatos de
  número viajan fila por fila, exactamente como el original. Probado en el consolidado 2026.
- Fórmulas nuevas SIEMPRE con un solo "=" inicial, verificadas en una celda de prueba antes
  de aplicar en masa (lección L-002). Al reescribir fórmulas jerárquicas para otra columna,
  sustituir SOLO la letra de columna (regex `\bH(\d+)` → nueva letra) y verificar que no
  haya referencias absolutas `$H$` ni referencias externas `[n]`.
- Donde había fórmula, sigue habiendo fórmula (regla de oro 3). Los meses futuros quedan
  formulados aunque hoy no tengan datos — así ella pega el dato y el cuadro se calcula solo:
  en filas de DETALLE, las celdas de meses futuros van VACÍAS (ella escribe el dato) y en
  filas de SUBTOTAL van FORMULADAS (la jerarquía se calcula sola). La columna acumulada es
  `=SUM(primer_mes:último_mes)` en todas las filas.
- Si se agregan filas/columnas, copiar el estilo de una fila/columna vecina existente.
- Nada de "limpiar" valores sin autorización expresa (lecciones L-003, L-007).
- Códigos que no existen en un mes o celdas sin valor cacheado: se dejan VACÍAS y se
  reportan — nunca se ponen ceros inventados (la verificación contra cifras de control
  confirma que no afectan los totales).

```python
# Escribir una fórmula y conservar estilo
celda = ws["L124"]
celda.value = "=SUMA(L2:L123)"      # ojo: según el locale del archivo puede ser SUM en el XML
# Si el archivo original usa funciones en español (SUMA, CONCATENAR), revisar cómo están
# almacenadas en el XML: Excel las guarda SIEMPRE en inglés en el archivo (SUM), y la
# interfaz las muestra traducidas. Escribir en el XML en inglés, verificar el resultado.
```

---

## PROTOCOLO 4 — CIRUGÍA XML (archivos oficiales con objetos incrustados)

Cuándo: si el archivo tiene objetos OLE/Visio/EMF, gráficos complejos o cualquier cosa que
openpyxl podría perder al guardar (lección L-001).

Patrón probado en el proyecto:
1. Copiar el xlsx a una carpeta de trabajo y descomprimirlo con `zipfile`.
2. Localizar y editar SOLO el XML de la hoja implicada (`xl/worksheets/sheetN.xml`).
3. **Expandir shared formulas** a fórmulas completas antes de mover/copiar celdas (lección L-004).
4. No tocar `styles.xml` (los colores y formatos viven ahí) salvo que la tarea lo pida.
5. Recomprimir preservando la estructura interna del ZIP.
6. Verificar: abrir el resultado con openpyxl Y con LibreOffice (Protocolo 5).

---

## PROTOCOLO 5 — VERIFICACIÓN (obligatoria antes de entregar)

1. **Original intacto**: sha256 del original idéntico al registrado al inicio.
2. **Doble vía para cifras críticas** (probada en el consolidado 2026):
   - **Vía A (al centavo)**: suma bottom-up de los valores de detalle escritos, comparada
     contra las cifras de control extraídas del original ANTES de editar (Protocolo 2).
     Esta vía valida el mapeo de códigos al centavo.
   - **Vía B (fórmulas vivas)**: recalcular el archivo construido con LibreOffice
     (`soffice --headless --convert-to csv ...`) y comparar los resultados calculados.
     OJO: el CSV de LibreOffice exporta el número con formato general (separadores estilo
     EE.UU. y sin decimales garantizados) — sirve para validar fórmulas y orden de
     magnitud; la verificación AL CENTAVO se hace en la Vía A con los valores cacheados.
3. **El resto del archivo no cambió**: comparar contra el original las zonas que la tarea
   no tocaba (fórmulas, colores, dimensiones).
4. **Prueba de apertura**: abrir el resultado con openpyxl (¿abre sin error?) y, si hay
   LibreOffice, convertirlo a PDF: `soffice --headless --convert-to pdf resultado.xlsx`
   para revisión visual del formato.
5. **Diferencias reportables**: separar las diferencias que introduces tú de las que ya
   venían en el original (lección L-007) y decirlo con claridad.
6. **Verificación fila por fila**: cuando el archivo tenga una columna calculada (ej.:
   acumulado), verificar TODAS las filas, no solo los totales.
7. **Última barrera**: este protocolo lo ejecuta quien construyó. Antes de entregar, pasar
   el resultado por la revisión independiente de `AGENTE-REVISOR.md` (segunda pasada, con
   OTRO método, con informe y veredicto). Solo se entrega lo aprobado.

---

## PROTOCOLO 6 — ENTREGA A LA USUARIA

0. **Puerta de calidad**: solo se entrega con veredicto ✅ o ⚠️ de `AGENTE-REVISOR.md`.
   Con veredicto ❌, se corrige y se re-revisa ANTES de escribirle una sola palabra a la
   usuaria. Ella nunca ve borradores.
1. **Nombre del archivo de salida**: conservar el nombre original y agregar sufijo
   identificable (ej.: `...PARA FIRMAR V1.xlsx` → `...PARA FIRMAR V2.xlsx`). Confirmar con
   ella la convención que prefiere (pendiente en PERFIL-MAMA.md).
2. **Entregar el archivo descargable** desde el chat (en Z.ai los archivos que genera el
   agente quedan disponibles para descargar junto al mensaje).
3. **Narración en su idioma**, siempre con esta estructura:
   - Qué hice (1-2 líneas)
   - Qué encontré (cifras en su jerga: "28 órdenes sin CRP", no "28 filas sin match")
   - Qué debería revisar ella (puntos de atención)
   - Qué quedó pendiente o qué preguntarle
4. Si la tarea fue grande, anunciar el tiempo estimado y avisar cuando termine.

---

## ÁRBOL DE DECISIÓN RÁPIDO

| La tarea pide... | Técnica |
|---|---|
| Leer datos, contar, sumar, filtrar | pandas (read_only) + narración simple |
| Cruzar dos archivos por clave | pandas merge + comparación de valores |
| Editar celdas/fórmulas/colores del formato original | openpyxl sobre copia |
| Agregar columnas formuladas conservando estilo | openpyxl + copia de estilo de celda vecina |
| Intervenir archivo con objetos incrustados | Cirugía XML (Protocolo 4) |
| Verificar que todo quedó bien | Protocolo 5 completo |
| Antes de mostrar cualquier resultado a la usuaria | Revisión de `AGENTE-REVISOR.md` (veredicto obligatorio) |
| Archivo de 1M+ filas | read_only / por trozos / cirugía XML; nunca cargar completo |

---

## PROTOCOLO DE ESTE ARCHIVO

- Actualizar cuando: cambien las herramientas disponibles del entorno, se aprenda una
  técnica nueva que funcione, o una lección de LECCIONES.md demuestre que un protocolo
  debe cambiar.
- Toda técnica documentada aquí debe estar respaldada por un caso real del proyecto o una
  lección; no documentar teoría no probada.
- La versión sube 0.1 por cada ajuste; registrar la fecha.
