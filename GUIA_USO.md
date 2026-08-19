# Guía de uso

Cómo preparar el `.blend` y qué puede controlar luego quien use la app. Documento vivo, se irá
ampliando.

## 1. Varias versiones/fases que se pueden alternar (Estados)

¿Quieres que la persona que use la app pueda pulsar un botón y cambiar entre, por ejemplo,
"solo estructura" y "con fachada"?

- Agrupa cada versión completa (todas sus piezas) en un único objeto por versión.
- Nombra cada uno `st1`, `st2`, `st3`... (el número decide el orden de los botones).
- Si quieres que el botón muestre un texto en vez del número, añádelo después de un guion bajo:
  `st2_con_fachada` → el botón dirá "con_fachada".
- Hacen falta mínimo 2 para que aparezca el selector "Estados" en el menú.
- Solo se ve una versión a la vez; el resto se oculta sola.

## 2. Partes que se encienden/apagan sueltas, con su propio color (Capas)

Para partes del modelo que se puedan mostrar/ocultar de forma independiente (no excluyentes
entre sí, a diferencia de los Estados) y a las que además se les pueda cambiar el color desde
la app:

- Agrupa cada parte en un objeto y nómbralo `ly1_estructura`, `ly2_fachada`, `ly3_mobiliario`...
- Cada una aparece como un interruptor independiente en "Capas", con su propio selector de
  color.

## 3. Plano que recibe la sombra del modelo

- Crea un plano (del tamaño que quieras que abarque la sombra) y llámalo `_shadow`.
- Da igual dónde lo coloques dentro de la jerarquía del `.blend`, se encuentra igual.

## 4. Piezas rellenas al hacer un corte (Sección)

La app tiene una herramienta de corte (planta/alzado/perfil). Si quieres que el hueco del corte
se vea relleno de color en vez de hueco (como una losa cortada de verdad):

- Nombra esas piezas con `_cutter` en cualquier parte del nombre, ej. `muro_cutter`.
- Esas piezas nunca se ven enteras: solo se usan para generar el relleno de color del corte.

## 5. Animación

Cualquier animación que metas en Blender se reproduce sola en cuanto carga el modelo. No hace
falta nombrarla de ninguna forma especial.

## 6. Reglas rápidas de nombres

- `st` y `ly` van pegados al número, sin espacio ni guion: `st1`, no `st 1` ni `st-1`.
- El texto del botón (opcional) va después de un guion bajo: `st2_fachada`.
- Si un `st..`/`ly..` queda dentro de otro `st..`/`ly..`, solo cuenta el de fuera — evita
  anidarlos.

## 7. Qué controla el usuario directamente en la app (nada que preparar en Blender)

- **Estados / Capas**: los botones e interruptores que generas nombrando arriba.
- **Visualización**: sombreado, alámbrico, o los dos a la vez; color del alámbrico.
- **Iluminación**: intensidad ambiente/direccional, dirección del sol, sombras on/off.
- **Sección**: activar el corte, elegir planta/alzado/perfil, posición y lado del corte.
- **Estabilidad**: cuánto tiembla o se suaviza el modelo sobre el marcador.
- **Modelo y marcador**: elegir otro `.glb`/`.mind` propio y generar un link de prueba para
  compartir (de momento solo funciona en el mismo dispositivo que lo genera).

## 8. Ya no existe

- `_marker_ref`: en una versión antigua servía para calibrar automáticamente la escala del
  modelo contra el ancho real del marcador. Ya no se usa — el tamaño y la posición del modelo
  se respetan tal cual salen de Blender, así que calibra tú la escala antes de exportar
  (compárala contra el ancho real de tu marcador impreso).
