# INFORME DE PATRONES DE USO

- Versión: 1.0
- Última actualización: 2026-10-08
- Qué es: el informe vivo de cómo trabaja la usuaria, construido con evidencia de las
  sesiones (no con impresiones). Sirve para adaptar el asistente a ella y detectar mejoras.
- Cuándo se actualiza: cada ~5 sesiones con uso real, o antes si aparece un patrón claro.
- Base de evidencia actual: 1 sesión de análisis + corpus de 13 archivos Excel reales.

---

## 1. RESUMEN EJECUTIVO (estado v0)

- El trabajo de la usuaria es **mensual y variable**: gira alrededor de conciliaciones,
  informes tesorales y consolidados, pero los procesos exactos cambian de mes a mes.
- Su carga pesada actual es la **conciliación OP×CRP** (proceso A): es el flujo con más
  volumen de datos y más pasos manuales.
- Un proceso (cuadro mensual consolidado, proceso C) **ya fue automatizado con éxito
  verificable al centavo** — la prueba de que el enfoque funciona.
- El objetivo del asistente no es reemplazarla: es liberarle las tareas repetitivas y
  precisas para que ella dedique su expertise a lo que requiere criterio.

## 2. PROCESOS POR FRECUENCIA ESTIMADA

| Proceso | Frecuencia | Volumen de datos | Estado de automatización |
|---|---|---|---|
| A. Conciliación OP×CRP | Mensual | Alta (20K-1M filas) | Motor validado, flujo por pulir |
| B. Informe tesoral | Mensual | Media | Pendiente |
| C. Cuadro mensual consolidado | Mensual | Media (97 filas) | Automatizado y verificado |

## 3. PATRONES DE INTERACCIÓN OBSERVADOS

- Describe peticiones como **proceso + resultado esperado**, con detalle de negocio pero
  sin terminología técnica (ver ejemplos en la sección 4).
- Usa frases de confirmación parcial: aprueba unas partes y corrige otras ("en la pregunta
  1 no entendí... con la pregunta 2 sí por favor límpialos... con rendimientos estoy de
  acuerdo"). El asistente debe responder punto por punto.
- Pide **continuidad de formato**: "respetando la fórmula que tiene del cuadro original con
  sus colores", "queden formuladas para cuando llegue el mes".
- Valora el **hallazgo proactivo**: los 76 valores fantasma descubiertos sin que los pidiera
  generaron confianza.

## 4. FRASES TÍPICAS (para reconocer peticiones)

- "Necesito una conciliación... un cruce con los erps..."
- "...necesito que se cree un archivo donde halle las diferencias que están en colores"
- "...que me arroje un informe tesoral... por fuente por valor mes"
- "...respetando la fórmula que tiene del cuadro original con sus colores"
- "...me adicione las columnas octubre noviembre y diciembre del recaudo y queden formuladas
  para cuando llegue el mes yo coloque los datos y me vaya calculando"

## 5. DOLORES Y ESTRESORES DETECTADOS

- Volumen manual de cruce de decenas de miles de filas por mes.
- Riesgo permanente de errores de centavos en archivos oficiales que se firman.
- [POR CONFIRMAR] Tiempo real que le toma cada proceso (preguntar y registrar).

## 6. TIEMPO AHORRADO ESTIMADO

Método: en cada sesión registrar qué se automatizó y una estimación del tiempo manual
equivalente. Aún sin datos: la primera medición será de la primera conciliación que ella
haga con el asistente.

## 7. OPORTUNIDADES DE MEJORA DETECTADAS

- Convertir el proceso A en un flujo de 1 clic (subir 2 archivos → descargar diferencias
  consolidadas) — es la Fase 1 del plan del proyecto.
- Un dashboard del informe tesoral sin depender de instalar Power BI — Fase 2.
- El "modo aprendiz": detectar cuando sube los mismos archivos juntos cada mes y proponer
  guardar el proceso como receta.

## 8. PREGUNTAS PARA LA PRÓXIMA SESIÓN (agenda viva)

1. ¿Qué significa cada color que usa para marcar diferencias en el CRUCE?
2. ¿El "archivo de RPs" es un archivo acumulado mes a mes? ¿Cuál es la versión más reciente?
3. ¿Qué descarga exactamente cada mes y de dónde (sistemas) salen los insumos?
4. ¿Necesita el .pbix obligatoriamente o le sirve dashboard web + exportación a Excel?
5. ¿Cuál de los 3 procesos le quita más tiempo al mes?
6. [Para el usuario] ¿Hay restricción formal de la universidad sobre datos sensibles en la nube?
