# Guía de uso

Cómo preparar el `.blend` y qué puede controlar luego quien use la app. Documento vivo, se irá
ampliando.

## 1. Escala y posición respecto al marcador (`_marker_ref`)

Este es el primer problema a resolver con cualquier modelo nuevo: por defecto la app no tiene
forma de saber qué tamaño real tiene tu marcador impreso ni dónde quieres que el modelo quede
apoyado sobre él. Sin esto, el modelo puede salir gigante, minúsculo o descolocado — y esto se
arregla SIN tener que achicar ni mover el modelo en sí.

**Qué hacer en Blender:**

1. Crea un plano o una caja simple, con el tamaño REAL de tu marcador impreso, medido en las
   mismas unidades que uses para modelar (si modelas en metros y el marcador impreso mide 30cm
   de ancho, esa pieza debe medir 0.3 en su lado más largo).
2. Colócala donde quieras que el marcador quede, en la realidad, respecto al modelo — por
   ejemplo pegada a la base de una escalera, o al lado de una fachada. El centro de esa pieza es
   el punto exacto donde luego "engancha" el marcador en AR.
3. Nómbrala con `_marker_ref` en el nombre (ej. `plano_marker_ref`).
4. Exporta normal. La app la usa una sola vez al cargar el modelo para calcular escala y
   posición, y luego la borra sola — nunca se llega a ver.

Con esto puedes tener un modelo tan grande como una escalera real y un marcador de tamaño A4, y
que en AR aparezca a su tamaño real en vez de encogido para caber entero en pantalla.

**Si no pones `_marker_ref`**: se respeta la escala/posición tal cual venga del GLB, sin ningún
ajuste automático — si el modelo sale mal de tamaño, hay que corregirlo a mano en Blender.

**Si el modelo calibrado a tamaño real es demasiado grande para caber en el encuadre** (p. ej.
una escalera completa, que solo se vería entera si te alejas mucho del marcador — a veces
imposible en un local pequeño): usa el slider "Escala" en Visualización dentro de la app — ver
sección 8. Deja intacta la calibración real, solo la reduce para verla más pequeña.

## 2. Varias versiones/fases que se pueden alternar (Estados)

¿Quieres que la persona que use la app pueda pulsar un botón y cambiar entre, por ejemplo,
"solo estructura" y "con fachada"?

- Agrupa cada versión completa (todas sus piezas) en un único objeto por versión.
- Nombra cada uno `st1`, `st2`, `st3`... (el número decide el orden de los botones).
- Si quieres que el botón muestre un texto en vez del número, añádelo después de un guion bajo:
  `st2_con_fachada` → el botón dirá "con_fachada".
- Hacen falta mínimo 2 para que aparezca el selector "Estados" en el menú.
- Solo se ve una versión a la vez; el resto se oculta sola.

## 3. Partes que se encienden/apagan sueltas, con su propio color (Capas)

Para partes del modelo que se puedan mostrar/ocultar de forma independiente (no excluyentes
entre sí, a diferencia de los Estados) y a las que además se les pueda cambiar el color desde
la app:

- Agrupa cada parte en un objeto y nómbralo `ly1_estructura`, `ly2_fachada`, `ly3_mobiliario`...
- Cada una aparece como un interruptor independiente en "Capas", con su propio selector de
  color.

## 4. Plano que recibe la sombra del modelo

- Crea un plano (del tamaño que quieras que abarque la sombra) y llámalo `_shadow`.
- Da igual dónde lo coloques dentro de la jerarquía del `.blend`, se encuentra igual.

## 5. Relleno sólido al hacer un corte (Sección) — opcional, avanzado

La app tiene una herramienta de corte (Planta/Alzado/Perfil): mueves un plano por el modelo y
todo lo que queda a un lado se oculta. Donde el plano atraviesa el modelo, la app intenta pintar
esa franja de un color sólido (rosa), como en una sección de arquitectura de verdad — para que
un muro cortado se vea como una franja rellena, no como un hueco vacío.

**Por defecto ya funciona sin hacer nada especial**: usa tu geometría normal para pintar ese
relleno. Solo hace falta `_cutter` si ese relleno automático te sale mal — por ejemplo, si tus
muros/losas están modelados como una simple lámina plana sin grosor real (habitual para que el
modelo pese menos), el corte por ahí no tiene "carne" que rellenar y sale una línea casi vacía en
vez de una franja sólida.

Para arreglar eso: duplicas esa pieza en Blender pero en versión **sólida** (con su grosor real,
puede ser una caja simple), la colocas en el mismo sitio que la pieza fina original, y la nombras
con `_cutter` (ej. `muro_cutter`). Esa copia sólida se queda siempre oculta en la vista normal —
solo aparece rellenando el corte, justo por donde pasa el plano, en el sitio exacto donde está.

Si no te importa que el relleno del corte no sea perfecto, puedes ignorar esto por completo.

## 6. Animación

Cualquier animación que metas en Blender se reproduce sola en cuanto carga el modelo. No hace
falta nombrarla de ninguna forma especial.

## 7. Reglas rápidas de nombres

- `st` y `ly` van pegados al número, sin espacio ni guion: `st1`, no `st 1` ni `st-1`.
- El texto del botón (opcional) va después de un guion bajo: `st2_fachada`.
- Si un `st..`/`ly..` queda dentro de otro `st..`/`ly..`, solo cuenta el de fuera — evita
  anidarlos.
- Solo puede haber un `_marker_ref` por modelo; si hay varios, se usa el primero que encuentre.

## 8. Qué controla el usuario directamente en la app (nada que preparar en Blender)

- **Estados / Capas**: los botones e interruptores que generas nombrando arriba.
- **Visualización**: sombreado, alámbrico, o los dos a la vez; color del alámbrico; slider
  "Escala" para ver el modelo más pequeño que su tamaño calibrado (100% = tamaño real de
  `_marker_ref`, hacia abajo lo va reduciendo) — útil cuando el modelo real no cabe entero en la
  cámara a una distancia razonable del marcador. No toca la calibración, solo la vista.
- **Iluminación**: intensidad ambiente/direccional, dirección del sol, sombras on/off.
- **Sección**: activar el corte, elegir planta/alzado/perfil, posición y lado del corte.
- **Estabilidad**: cuánto tiembla o se suaviza el modelo sobre el marcador.
- **Modelo y marcador**: elegir otro `.glb`/`.mind` propio y generar un link de prueba para
  compartir (de momento solo funciona en el mismo dispositivo que lo genera).
