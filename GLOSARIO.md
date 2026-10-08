# GLOSARIO DEL NEGOCIO

- Versión: 1.0
- Última actualización: 2026-10-08
- Propósito: hablar el idioma de la usuaria. Cada término nuevo que ella use se agrega aquí
  tras confirmar su significado.

---

## SIGLAS Y TÉRMINOS PRESUPUESTALES

| Término | Significado simple | Notas |
|---|---|---|
| **OP** | Orden de Pago: documento con el que la universidad gira (ordena) un pago | Aparece en el cuadro mensual del sistema (~28.914 filas/mes) |
| **CRP** | Certificado de Registro Presupuestal: registro en el ERP contra el que se cruza la OP | Llega en el extracto "CRP Y OP [mes]" |
| **RP** | Registro Presupuestal: afectación presupuestal asociada al compromiso | Las diferencias del cruce "se adicionan al archivo de RPs" |
| **CDP** | Certificado de Disponibilidad Presupuestal: reserva de presupuesto antes del compromiso | Usado en el flujo; precisión de su rol [POR CONFIRMAR] |
| **Fuente** | Fuente de financiación del dinero (ej.: Funcionamiento, Inversión, Estampilla) | Las consolidaciones siempre se hacen "por fuente" |
| **Vigencia** | Año presupuestal al que pertenece el gasto | Aparece en las claves de cruce |
| **SHD** | Término que aparece en la conciliación (contrapartida del pagado) | Significado exacto [POR CONFIRMAR] |

## TÉRMINOS DE SU FLUJO DE TRABAJO

| Término | Significado simple | Notas |
|---|---|---|
| **Conciliación** | Informe de las OP canceladas cruzadas contra el ERP, con sus diferencias consolidadas | Proceso A |
| **Cruce** | Emparejar las OP contra el CRP usando la clave concatenada | El resultado va al archivo "CRUCE [mes]" |
| **Clave concatenada** | Cadena única construida con CONCATENAR a partir de varios campos (ej.: `11659O21202020080686312`) | Es la llave del cruce; vive en el archivo CRUCE |
| **Diferencias** | Órdenes que no cuadran: sin CRP, CRP sin orden, o valores distintos | Ella las marca con colores |
| **Valores fantasma** | Valores pegados donde deberían ir fórmulas; contaminan acumulados si no se limpian | 76 detectados en OCT/NOV del cuadro mensual |
| **Cuadro mensual** | Consolidado del recaudo por mes (ENE→DIC) con acumulado de tesorería | Proceso C, ya automatizado |
| **Recaudo** | Dinero efectivamente recaudado/pagado en el mes | Se consolida por mes y por fuente |
| **Informe tesoral** | Informe de OP canceladas por fuente, valor y mes | Proceso B, su "Power BI" |
| **ERP** | Sistema financiero de la universidad del que sale el extracto CRP | — |
| **CONCATENAR** | Función de Excel que une textos; base de la clave de cruce | — |

## TÉRMINOS PENDIENTES DE AGREGAR

(Día a día aparecerán más. Al confirmar un término nuevo, agregar fila en la tabla
correspondiente y subir la versión de este archivo.)

- [POR CONFIRMAR] Significado de cada color que ella usa para marcar diferencias.
- [POR CONFIRMAR] Más siglas de la universidad que aparezcan en sus archivos.

---

## PROTOCOLO DE ESTE ARCHIVO

- Cuando la IA detecte un término que no esté aquí, preguntar "¿qué significa [término]
  para ustedes?" y agregarlo tras confirmar.
- Definiciones al nivel de "explicación simple para la propia usuaria", no definiciones de manual.
