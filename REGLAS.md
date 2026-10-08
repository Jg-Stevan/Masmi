# REGLAS DE ORO

- Versión: 1.3
- Última actualización: 2026-10-08
- Alcance: TODA operación del asistente sobre archivos de la usuaria, en cualquier chat.

---

Estas reglas heredan la experiencia del chat que automatizó el cuadro mensual (donde se
verificaron cifras al centavo) y de los errores ya cometidos y corregidos. **No se
modifican ni se quitan sin confirmación explícita del usuario.** Agregar reglas nuevas sí
está permitido con su aprobación.

## LAS 8 REGLAS

1. **Nunca modificar el diseño ni el formato de los archivos oficiales.** Los colores,
   fórmulas, hojas y estructura son parte del documento. Si hay que intervenir un archivo
   oficial, se opera con técnicas que preserven todo (cirugía XML cuando openpyxl no
   alcanza) y se verifica la fidelidad después.

2. **Nunca inventar datos.** Toda cifra que se reporte debe venir de los archivos reales y
   ser rastreable. Si un dato no existe o no se puede calcular, se dice y se pregunta.

3. **Todo formulado.** Donde el cuadro original tiene fórmulas, el resultado las mantiene
   o las replica. Nunca se pegan valores donde van fórmulas (los "valores fantasma" son un
   defecto, no un método).

4. **Toda transformación genera un archivo NUEVO descargable.** Los originales quedan
   intactos, siempre. Verificación de integridad: huella (sha256) de los originales antes
   y después de cualquier operación.

5. **Confirmar antes de ejecutar operaciones grandes.** Describir en lenguaje simple qué
   se va a hacer ("voy a cruzar estos dos archivos y marcar en amarillo las diferencias")
   y esperar el OK de la usuaria o del usuario.

6. **Doble verificación de cifras críticas.** Los totales y diferencias importantes se
   validan por dos vías independientes (ej.: suma directa y suma por grupos) antes de
   reportarse. Si no cuadran, no se reportan hasta entender por qué.

7. **Nunca borrar ni "limpiar" sin preguntar.** Lo que parece un "valor fantasma" o un dato
   sobrante puede ser una fórmula, un encabezado o algo que la usuaria dejó a propósito.
   Se pregunta primero. (Lección aprendida: se borraron encabezados por tratarlos como
   valores fantasma.)

8. **Con la usuaria final, español simple y cero tecnicismos.** Los resultados se narran en
   su idioma ("28 órdenes giradas sin CRP"), nunca en lenguaje técnico.

## REGLAS OPERATIVAS ADICIONALES (de la práctica)

9. **Archivos gigantes**: nunca cargar en memoria completa archivos de cientos de miles de
   filas. Perfilado perezoso (encabezados + muestra + conteos) y lectura por trozos.

10. **Después de reinicios del entorno**: verificar que las rutas y archivos del proyecto
    sigan existiendo antes de asumir que todo está. Git es la red de seguridad. (Lección
    del 404 en la subida, octubre 2026.)

11. **Privacidad**: los archivos contienen datos sensibles (cédulas, nombres, cuentas).
    No replicar datos sensibles en documentación, logs ni memoria. Tratarlos solo en el
    entorno de trabajo y solo cuando el proceso lo requiera.

12. **Contrato de entrega (definido por el usuario, 2026-10-08)**: ella sube un Excel y pide
    algo; el asistente devuelve el archivo EN EL MISMO FORMATO con la tarea realizada.
    Se trabaja siempre sobre una copia, el original no se toca, y si el resultado no puede
    ser un xlsx con el formato idéntico, se explica ANTES de ejecutar, no después.

13. **Revisión independiente antes de toda entrega (definida por el usuario, 2026-10-08)**:
    ningún trabajo llega a la usuaria sin una segunda pasada de revisión (`AGENTE-REVISOR.md`)
    que verifique errores, incongruencias con lo pedido, mejoras posibles y sugerencias.
    Lo crítico se corrige ANTES de mostrar; las sugerencias se proponen, nunca se aplican
    sin aprobación. El revisor usa un método distinto al que construyó el resultado.

14. **Higiene del token de GitHub (si está configurado)**: el token solo vive en la sección
    CONFIGURACIÓN de `SYNC-GITHUB.md` y en la variable de entorno del comando; JAMÁS se
    imprime en respuestas, diarios, informes ni logs. Al repo solo van los .md de la
    memoria — nunca Excels ni datos de la usuaria (regla 11). Si el token expira o falla
    (401/403), se informa al usuario y se espera uno nuevo, sin reintentar en bucle.

---

## PROTOCOLO DE ESTE ARCHIVO

- Agregar regla: solo con aprobación explícita del usuario. Numerar consecutivamente.
- Modificar o retirar una regla: requiere justificación + confirmación del usuario +
  registro de la lección que la motivó en `LECCIONES.md`.
- Cada cambio sube la versión y actualiza "Última actualización".
