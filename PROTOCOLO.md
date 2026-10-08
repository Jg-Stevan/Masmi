# PROTOCOLO DE LA MEMORIA

- Versión: 1.3
- Última actualización: 2026-10-08
- Para quién: cualquier IA (o persona) que opere con esta memoria portátil

---

## PRINCIPIO FUNDAMENTAL

Esta memoria existe para que ningún chat tenga que empezar de cero y para que el asistente
mejore con el uso. La memoria sirve a la usuaria, no al revés: si actualizarla compite con
ayudarla en algo urgente, primero se ayuda, luego se actualiza.

---

## CÓMO USAR LOS ARCHIVOS ANTE CADA TIPO DE PETICIÓN

| Si la usuaria/usuario pide... | Consulta primero | Consulta también |
|---|---|---|
| Leer, editar o devolver un Excel | `HERRAMIENTAS-EXCEL.md` | `REGLAS.md`, `TRABAJO.md` |
| Vas a entregar cualquier resultado (archivo, cifras, informe) | `AGENTE-REVISOR.md` | `REGLAS.md`, `HERRAMIENTAS-EXCEL.md` |
| Empieza una sesión y hay repo configurado | `SYNC-GITHUB.md` | `INICIO.md` (pull de mejoras) |
| Una conciliación, cruce o informe | `TRABAJO.md` | `GLOSARIO.md`, `REGLAS.md` |
| Algo con cifras o validación | `REGLAS.md` | `TRABAJO.md` (cifras ya validadas) |
| Decide cómo hablarle a ella | `PERFIL-MAMA.md` | `PATRONES-DE-USO.md` |
| No entiende un término suyo | `GLOSARIO.md` | — |
| Vas a tocar archivos oficiales | `REGLAS.md` + `LECCIONES.md` | `TRABAJO.md` |
| Dudas "cómo se hace esto aquí" | `LECCIONES.md` | `INICIO.md` (estado del proyecto) |

---

## DISPARADORES DE ACTUALIZACIÓN (el corazón de la auto-actualización)

| Evento durante la sesión | Archivo a actualizar | Cómo |
|---|---|---|
| Aparece un proceso o flujo nuevo de ella | `TRABAJO.md` | Agregarlo con la plantilla de procesos |
| Se aprende una técnica nueva de Excel o cambia el entorno | `HERRAMIENTAS-EXCEL.md` | Actualizar el protocolo correspondiente |
| Aparece un término nuevo de su jerga | `GLOSARIO.md` | Confirmar significado con ella/usuario y agregarlo |
| La IA se equivoca y corrige | `LECCIONES.md` | Plantilla de lección (append) |
| Se descubre algo técnico valioso | `LECCIONES.md` | Plantilla de lección (append) |
| Ella confirma un dato personal o preferencia | `PERFIL-MAMA.md` | Llenar el campo correspondiente |
| Se confirma qué significa un color o marca | `TRABAJO.md` | Completar la sección de colores |
| El usuario aprueba una regla nueva | `REGLAS.md` | Agregarla como regla numerada |
| Una revisión del AGENTE REVISOR encuentra errores o trampas nuevas | `LECCIONES.md` | Plantilla de lección (append); ajustar el protocolo afectado si aplica |
| Se completa una entrega con veredicto de revisión | `DIARIO.md` | Veredicto y hallazgos en la entrada de la sesión |
| Cierra una sesión con trabajo real | `DIARIO.md` | Entrada de 3-8 líneas (append) |
| Se cierra la sesión y hay repo configurado | Repo de GitHub (SYNC-GITHUB.md) | Push de los .md actualizados + link del commit en la respuesta |
| Se completan 5 sesiones o aparece un patrón | `PATRONES-DE-USO.md` | Actualizar secciones y fecha del informe |

---

## REGLAS DE ESCRITURA DE LA MEMORIA

1. **Append-only en historia**: `DIARIO.md` y `LECCIONES.md` nunca borran entradas. Se corrige agregando, no reescribiendo.
2. **Propuesta antes de escribir**: toda actualización se anuncia en 1-3 líneas ("Voy a agregar a TRABAJO.md el proceso X") y se escribe tras el OK. Al cierre de sesión se agrupan todas las propuestas pendientes.
3. **Cabeceras vivas**: cada archivo lleva `Versión` y `Última actualización`. Cambio menor suma 0.1; cambio estructural suma 1.0.
4. **Formato de fecha**: siempre AAAA-MM-DD.
5. **Datos sin confirmar**: se marcan `[POR CONFIRMAR]`, nunca se inventan.
6. **Escribe para el próximo lector**: otra IA sin contexto, o el usuario dentro de meses. Explica suficiente para que se entienda sin preguntar.
7. **Nada de datos sensibles innecesarios**: la memoria describe procesos y lógica; no sirve para almacenar cédulas, cuentas ni nombres de beneficiarios.
8. **Idioma**: español de Colombia, claro. Los términos técnicos van con su explicación simple.

---

## RITUAL DE CIERRE DE SESIÓN (obligatorio cuando hubo trabajo real)

1. **Resumen** de lo hecho en máximo 3 líneas.
2. **Propuesta de actualización de memoria**: qué archivos y qué cambios concretos.
3. **Escritura** de los cambios tras confirmación (o guardarlos como pendientes si el usuario no responde).
4. **Próximo paso**: dejar escrito qué sigue para que el próximo chat lo retome.
5. Si el entorno lo permite, **regenerar el ZIP** de la memoria para que el usuario lo descargue actualizado.
6. Si `SYNC-GITHUB.md` tiene token configurado: **hacer push** de los .md actualizados al
   repo y reportar el link del commit (nunca el token). Si falla, anotarlo como pendiente
   de sincronizar y continuar.

---

## CÓMO GENERAR EL INFORME DE PATRONES

Cuando corresponda actualizar `PATRONES-DE-USO.md`:

1. Leer las últimas entradas de `DIARIO.md` y los cambios recientes de todos los archivos.
2. Actualizar cada sección del informe con evidencia (no con impresiones): frecuencia de procesos, frases típicas nuevas, dolores expresados, tiempo estimado ahorrado.
3. Terminar con la sección "Preguntas para la próxima sesión" — es la agenda del siguiente encuentro.
4. Si un patrón sugiere una mejora concreta del asistente, proponerla al usuario antes de escribirla como compromiso.

---

## RELACIÓN CON OTRAS DOCUMENTACIONES DEL PROYECTO

- `PLAN.md` (proyecto de desarrollo): roadmap técnico por fases. Esta memoria no lo duplica.
- `SESSION_STATE.md` / `worklog.md` (proyecto de desarrollo): estado técnico de la sesión de desarrollo.
- Esta carpeta: conocimiento del dominio, personalidad del asistente, reglas y aprendizaje acumulado.
- Si un chat nuevo recibe ambos paquetes (memoria + proyecto), leer `INICIO.md` primero y `PLAN.md` después.

---

## REGISTRO DE CAMBIOS DE ESTE PROTOCOLO

- 2026-10-08 v1.0: creación inicial con el diseño de memoria en 4 capas y protocolo de auto-actualización.
- 2026-10-08 v1.1: agregado HERRAMIENTAS-EXCEL.md al mapa de uso y a los disparadores (contrato de trabajo: subir Excel → pedir → devolver en el mismo formato).
- 2026-10-08 v1.2: agregado AGENTE-REVISOR.md (revisión independiente obligatoria antes de toda entrega, a pedido del usuario) al mapa de uso y a los disparadores.
- 2026-10-08 v1.3: agregado SYNC-GITHUB.md (sincronización con el repo privado del usuario: pull al inicio, push al cierre) al orden de lectura, mapa de uso, disparadores y ritual de cierre.
