# TRABAJO DE LA USUARIA — CONOCIMIENTO DEL DOMINIO

- Versión: 1.2
- Última actualización: 2026-10-08
- Fuente: análisis de la conversación "Creador de masmi", de los 13 Excel reales del corpus (3 ZIPs) y de la primera tarea real ejecutada (consolidado 2026, ver DIARIO).

---

## CONTEXTO LABORAL

- Tesorería de la Universidad Distrital (Bogotá, Colombia).
- Maneja mensualmente: órdenes de pago, conciliaciones contra el ERP, informes tesorales y
  consolidados de recaudo.
- Trabaja con Excel oficiales de gran tamaño: entre 28.000 y 1.027.000+ filas; archivos de
  3 a 31+ MB. El formato (colores, fórmulas, hojas) es parte del documento oficial.

---

## PROCESOS CONOCIDOS (3 hasta hoy — la lista crece con el uso)

### Proceso A — Conciliación de Órdenes de Pago × CRP (el más pesado)

**Objetivo**: informe de las órdenes de pago canceladas, hallando diferencias entre lo
girado (OP) y lo registrado en el ERP (CRP), y consolidando la información por fuente.

**Flujo documentado**:
1. Cuadro de órdenes de pago del sistema (~28.914 filas × 38 columnas).
2. Extracto ERP "CRP Y OP [MES]" (~20.852 filas × 54 columnas).
3. **Cruce por clave concatenada** (función CONCATENAR en ambos archivos). Ejemplo de clave:
   `11659O21202020080686312`.
   - Nota importante (lección 2026-10): la clave de cruce vive en el archivo CRUCE
     histórico, no siempre en el extracto crudo.
4. El archivo "CRUCE [mes]" muestra las diferencias marcadas con colores.
5. Esas diferencias se adicionan al archivo de RPs.
6. Se consolida la información por fuente: pendientes por solicitar / pagos realizados por
   la universidad / solicitado (RP vs presupuesto).

**Cifras reales validadas (cruce de agosto)** — usar como referencia de calibración:
- Órdenes de pago con CRP: 11
- Órdenes de pago sin CRP: 28.902
- CRPs sin orden asociada: 32
- Diferencias de valor: 3 (neta aproximada −$1.571 millones)
- Conciliación de agosto (fuente Funcionamiento): pagado $487.828.342.415 vs −$474.936.147.609
  SHD → saldo $12.892.194.806 en 10.321 órdenes.

**Pendiente de confirmar**: significado exacto de cada color en el archivo CRUCE
(qué color = qué tipo de diferencia).

### Proceso B — Informe Tesoral (su "Power BI")

**Objetivo**: informe de las órdenes de pago canceladas por la universidad, organizado por
fuente, valor y mes, más la relación completa descargable de todas las órdenes.

**Referencia real**: pivote mensual en su Hoja2 — enero $21.028M → julio $42.309M,
total $320.754M.

**Pendiente de confirmar**: si necesita el archivo .pbix de Power BI obligatoriamente o le
sirve un dashboard interactivo con exportación a Excel.

### Proceso C — Cuadro Mensual Consolidado (EJECUTADO y ENTREGADO; ver DIARIO 2026-10-08, entradas 3-4)

**Objetivo**: recaudo por columnas ENE→DIC respetando fórmulas y colores del cuadro
original, suma del recaudo acumulado de tesorería, y columnas OCT/NOV/DIC formuladas para
que cuando llegue el mes y ella coloque los datos, todo se calcule solo.

**Estado 2026-10-08 (v1.2)**: ejecutado sobre el consolidado oficial subido por ella (455 KB, sha256 d5da660e...353).
Receta validada (ver HERRAMIENTAS-EXCEL protocolos 3-5 y LECCIONES L-012..L-015):
1. Cirugía XML obligatoria (3 Visio + EMF + printerSettings). Nunca openpyxl para guardar.
2. Resolver fórmulas compartidas multi-columna a fórmulas concretas ANTES de re-letrar.
3. Insertar 8 columnas antes del mes actual + 1 (DIC) después del último mes; espejo de estilo por fila desde la columna del mes actual.
4. Datos por código de CADA archivo mensual para TODAS las filas (las filas vacías del mes base pueden tener datos en otros meses).
5. Acumulado =SUM(meses) en todas las filas con valor; OCT/NOV/DIC vacías en hojas, formuladas en subtotales.
6. Limpiar <v> de celdas movidas/vaciadas + fullCalcOnLoad="1"; borrar calcChain.
7. Verificar: acumulado fila 10 == cifra de control ($436.017.487.602,32), totales por mes == totales de los archivos fuente, recálculo LibreOffice sin cachés, integridad byte a byte de estilos y objetos.

**Estructura descubierta de los cuadros mensuales (formato GRF-PR-029-FR-029, hoja TOTAL)**:
- Columnas fijas: A=CÓDIGO (jerarquía 1 → 1.1.02.02.116.01.01.01), B=CONCEPTO.
- La posición de "RECAUDO MES TESORERIA" CAMBIA: columna G de enero a agosto; columna H en
  septiembre V1 (cuando se añadieron "RECAUDO ACUMULADO PRESUPUESTO/TESORERIA" y
  "DIFERENCIA RECAUDO MES"). Localizar SIEMPRE por encabezado (lección L-008).
- Filas 10-106: jerarquía con subtotales formulados en el mismo libro (ej.: fila 10
  INGRESOS = +fila 11 + 64 + 99) y detalles con valores o referencias a libros externos
  (prefijo `[n]Hoja!`). Filas 108-113: etiquetas "Rendimientos...". Filas finales: bloque
  de firmas (Tesorero).
- Los códigos crecen durante el año: enero tiene 99, septiembre 103.

**Estado**: la tarea del consolidado se ejecutó de prueba el 2026-10-08 con resultado
verificado al centavo (ver DIARIO). El consolidado "00" no venía en el ZIP y se construyó
desde los 9 mensuales:
- Acumulado de tesorería ENERO-SEPTIEMBRE 2026: **$436.017.487.602,32** (coincide con la
  cifra del chat anterior — doble validación del dato).
- Controles por mes (fila 10 INGRESOS): ENE $4.394.698.391 · FEB $35.992.892.373,44 ·
  MAR $51.992.211.233 · ABR $50.434.714.844 · MAY $56.402.773.043,43 · JUN $47.413.182.107,76
  · JUL $71.536.608.097 · AGO $73.779.313.166,69 · SEP $44.071.094.346.

---

## ARCHIVOS REALES DEL CORPUS (referencia de escala)

| Archivo | Rol | Escala |
|---|---|---|
| Órdenes de pago (mensual) | Insumo del cruce | ~28.914 filas × 38 cols |
| CRP Y OP [mes] | Extracto ERP | ~20.852 filas × 54 cols |
| CRUCE (histórico 2025) | Resultado del cruce | ~1.027.726 filas; incluye hoja RESERVAS (1.989 filas) |
| Conciliación mensual | Consolidado final | Comparación RP vs Tesorería vs Presupuesto por fuente |
| Cuadro mensual consolidado | Recaudo ENE→DIC | 97 filas; acumulado anual |

---

## LO QUE TODAVÍA NO SABEMOS (y cómo lo vamos a saber)

- Sus procesos son variables: la lista de arriba es una muestra, no el universo.
- No sabemos todos los reportes que le piden ni el origen exacto de cada insumo.
- **Método acordado**: en cada sesión, preguntarle qué va a hacer hoy, registrar aquí el
  proceso nuevo con la plantilla, y dejar que la memoria crezca con el uso.

---

## PLANTILLA PARA DOCUMENTAR UN PROCESO NUEVO

Cuando aparezca un proceso nuevo, documentarlo así en la sección "Procesos conocidos":

```
### Proceso [letra siguiente] — [nombre que ella le dé]
Objetivo: (en sus palabras)
Insumos: (archivos que sube, de dónde salen)
Flujo: (pasos numerados, con la lógica de negocio exacta)
Resultado esperado: (cómo se ve el archivo/ informe final, qué marcas lleva)
Validaciones: (cómo se sabe que quedó bien)
Cifras de referencia: (si hay, de un mes real)
Pendiente de confirmar: (lo que falte)
```
