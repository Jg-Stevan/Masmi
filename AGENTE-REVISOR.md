# AGENTE REVISOR — CONTROL DE CALIDAD ANTES DE ENTREGAR

- Versión: 1.0
- Creado: 2026-10-08 (a pedido del usuario, hijo e intermediario)
- Para quién: la IA que opere esta memoria, en cualquier chat. Define la última barrera
  de calidad antes de que la usuaria vea cualquier trabajo.

---

## LA IDEA EN UNA FRASE

**Ningún trabajo llega a la usuaria sin que una segunda pasada de revisión —hecha como si
la hiciera otra persona— lo verifique.** Esa pasada busca: errores, incongruencias con lo
que ella pidió, mejoras posibles del archivo y sugerencias. La usuaria ve trabajo
revisado, no borradores.

---

## CUÁNDO SE ACTIVA

- **SIEMPRE**, antes de mostrar a la usuaria (o al usuario) cualquier entregable:
  archivos Excel, cifras, informes, resúmenes o respuestas con datos.
- Momento exacto del flujo: **después del Protocolo 5** (verificación del que construyó)
  **y antes del Protocolo 6** (entrega) de `HERRAMIENTAS-EXCEL.md`.
- No aplica a conversación normal sin datos ni archivos (si hay cifras de por medio, aplica).

---

## QUIÉN REVISA Y CÓMO

- En entornos con varios agentes: **el agente que construyó NO es el que revisa**. Se usa
  un segundo agente revisor que recibe esta instrucción y el resultado a verificar.
- En chats de un solo agente: la misma IA hace una **SEGUNDA PASADA separada**, cambiando
  de rol ("ahora soy el revisor, no el autor") y, sobre todo, cambiando de método.
- **Regla de oro del revisor: no firmar su propio método.** Si el archivo se construyó con
  openpyxl, se verifica recalculando con LibreOffice o pandas. Si una cifra se obtuvo con
  una suma directa, se verifica con otra agrupación o desde los archivos fuente. El
  revisor no repite los pasos del autor: los contradice con evidencia.

---

## QUÉ REVISA (checklist obligatorio)

### 1. Fidelidad al contrato (regla 12)
- [ ] El entregable es un xlsx con la misma estructura de hojas del original.
- [ ] Filas, columnas, celdas combinadas y anchos: iguales salvo lo que la tarea pidió.
- [ ] Colores y formatos de número intactos en las zonas que no se tocaban.
- [ ] Objetos incrustados (imágenes, Visio, gráficos): presentes si el original los tenía.
- [ ] El original quedó intacto (sha256 idéntico al registrado en la recepción).

### 2. Congruencia con lo que ella pidió
- [ ] **CADA parte del pedido quedó resuelta** (leer el pedido de ella frase por frase y
      marcar cada parte contra el resultado).
- [ ] No se agregó nada que ella no pidió (columnas, hojas, formatos extra).
- [ ] Lo que debía quedar formulado tiene fórmulas reales, y lo que ella va a digitar
      quedó VACÍO, no con ceros (política de celdas vacías).

### 3. Matemática y fórmulas
- [ ] Los totales se recomputaron con un método independiente y cuadran al centavo.
- [ ] Las fórmulas nuevas apuntan a los rangos correctos (ni una celda corrida).
- [ ] Sin #REF!, #VALUE!, #NAME?, ni fórmulas rotas heredadas de shared formulas.
- [ ] Sin valores cacheados fantasma (celdas que muestran un número viejo sin fórmula real
      detrás).

### 4. Efectos secundarios
- [ ] Las zonas que la tarea no tocaba quedaron iguales al original (o la diferencia está
      explicada y es intencional).
- [ ] Si hubo cirugía XML: el ZIP interno conserva TODAS las partes del original
      (estilos, embeddings, drawings, printerSettings).
- [ ] El archivo abre sin errores con openpyxl Y con LibreOffice.

### 5. Congruencia de la narración
- [ ] Cada cifra que se le va a decir a ella aparece en el archivo o en la fuente.
- [ ] La narración está en su jerga y sin tecnicismos (OP, CRP, recaudo, tesorería).
- [ ] Las diferencias PREEXISTENTES del original están identificadas como tales, no como
      errores del asistente (lección L-007).

### 6. Higiene de entrega
- [ ] Nombre del archivo con el sufijo de la convención; el original no se sobrescribe.
- [ ] El peso del entregable es razonable frente al original (un salto enorme = algo se
      perdió o se duplicó).
- [ ] La respuesta empieza por lo que ella necesita saber, no por el detalle técnico.

---

## SEVERIDADES Y VEREDICTO

| Nivel | Qué es | Qué pasa |
|---|---|---|
| 🔴 CRÍTICO | Cifra mal, fórmula rota, formato perdido, dato inventado, objeto perdido | Se corrige ANTES de entregar y se re-revisa lo corregido. NO se muestra nada a la usuaria. |
| 🟡 IMPORTANTE | Incongruencia menor, tecnicismo en la narración, formato no crítico | Se corrige antes de entregar si es posible; si no se puede, se dice con claridad en la narración. |
| 🟢 SUGERENCIA | Mejora que ella NO pidió (columna útil, fórmula más robusta, mejora del archivo) | **NUNCA se aplica sin aprobación.** Se lista en "Sugerencias" para que ella decida. |

Veredictos posibles:

- ✅ **APROBADO** — sin críticos ni importantes. Se entrega.
- ⚠️ **APROBADO CON OBSERVACIONES** — solo sugerencias, o importantes ya explicados. Se entrega.
- ❌ **DEVUELTO PARA CORRECCIÓN** — tiene críticos (o importantes sin resolver). Vuelve al
  constructor, se corrige y **SE RE-REVISA** (solo los puntos afectados). El ciclo se repite
  hasta ✅ o ⚠️. Si el mismo punto falla 2 veces, parar y preguntar al usuario: puede ser un
  malentendido de la tarea y no un error de ejecución.

---

## EL INFORME DE REVISIÓN (formato fijo)

El revisor produce SIEMPRE este informe interno. A la usuaria NO se le muestra completo:
a ella le llega el resultado + la narración simple, y (si hay) las sugerencias en su idioma.

```
INFORME DE REVISIÓN — AAAA-MM-DD
Entregable: <nombre del archivo o respuesta>
Método del revisor: <con qué se verificó; debe diferir del método del constructor>
Veredicto: ✅ APROBADO / ⚠️ APROBADO CON OBSERVACIONES / ❌ DEVUELTO PARA CORRECCIÓN
Críticos: N | Importantes: N | Sugerencias: N
Checklist: 1✔ 2✔ 3✔ 4✔ 5✔ 6✔  (marcar los que fallan)
Hallazgos:
- 🔴 <qué, dónde, evidencia (cifras)>
- 🟡 <...>
- 🟢 <...>
Acción: corregido y re-revisado / entregado con observación / devuelto al constructor
```

**Anti-sello en vacío**: ningún check sin evidencia. Si el revisor no pudo verificar algo
(no hay con qué recomputar, no hay cifras de control), lo escribe explícitamente como
`NO VERIFICADO` y lo trata como 🟡. "No encontré errores" solo vale si hay evidencia de que
se buscó.

---

## DESPUÉS DE LA REVISIÓN

- El veredicto y los hallazgos se registran en la entrada del `DIARIO.md` de la sesión.
- Si un hallazgo revela una trampa nueva del formato o del entorno → lección nueva en
  `LECCIONES.md` (append) y, si corresponde, ajuste del protocolo afectado en
  `HERRAMIENTAS-EXCEL.md`.
- Las sugerencias 🟢 que la usuaria apruebe se aplican sobre una **NUEVA copia** (nunca
  sobre el entregable ya aprobado) y la nueva copia pasa de nuevo por revisión.

---

## PROTOCOLO DE ESTE ARCHIVO

- Este protocolo no reemplaza ni cambia los protocolos 1-6 de `HERRAMIENTAS-EXCEL.md`:
  se aplica DESPUÉS del 5 y ANTES del 6.
- Actualizar cuando: la práctica muestre un check que falta en el checklist, el veredicto
  o las severidades necesiten ajuste, o el usuario pida cambiar qué se revisa o cómo se
  reporta.
