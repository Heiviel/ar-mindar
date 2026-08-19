# Guía de uso

Documento vivo con las convenciones y flujos de trabajo de la app (`index.html`). El
[README.md](README.md) cubre instalación/deploy; esto cubre cómo usarla día a día. Se irá
ampliando.

## Convención de nombres dentro del GLB

`index.html` reconoce ciertos objetos del GLB por su **nombre** (no hace falta tocar código
para usar estados, capas, sombra o secciones — solo nombrar bien en Blender):

| Uso | Patrón de nombre | Dónde se aplica | Notas |
| --- | --- | --- | --- |
| Plano que recibe sombra | termina en `_shadow` (o es exactamente `shadow`) | cualquier mesh, en cualquier punto de la jerarquía | Admite el sufijo `.001` que añade Blender en duplicados (`_shadow.001`). El código lo saca automáticamente de su grupo padre, así que no se oculta si ese grupo (un estado/capa) se apaga. Ej.: `_shadow`, `suelo_shadow`. |
| Pieza de corte (para la sección) | contiene `_cutter`, `__cutter`, `[cutter]` o `.cutter` en cualquier parte del nombre | mesh | Se usa para generar la "tapa" de color en el plano de corte; el objeto en sí siempre queda oculto. Ej.: `muro_cutter`, `losa[cutter]`. |
| Estado (uno visible a la vez, botones en "Estados") | `st<numero>` o `st<numero>_<etiqueta>` | grupo/mesh raíz — si anidas un `st..` dentro de otro `st..`, el de dentro se ignora | El número ordena los botones; la etiqueta (si la hay) es el texto del botón, si no se usa el número. Hacen falta ≥2 para que aparezca el panel. Ej.: `st1`, `st2_con_fachada`. |
| Capa (se enciende/apaga suelta, con selector de color, en "Capas") | `ly<numero>` o `ly<numero>_<etiqueta>` | grupo/mesh raíz — misma regla de anidado que los estados | Ej.: `ly1_estructura`, `ly2_fachada`. |
| Animación | cualquier nombre | acción/clip de animación | No hace falta convención: todas las animaciones que traiga el GLB se reproducen automáticamente al cargar. |

Usa siempre el formato exacto `st<numero>` / `ly<numero>` (dígitos justo después, guion bajo
antes de la etiqueta si la pones) — un nombre que no encaje exactamente (p. ej. `st1b` sin guion
bajo) se sigue detectando pero cae al final del orden y usa el nombre completo como etiqueta.

`_marker_ref` (calibración de escala contra el marcador) **ya no existe**: el modelo se coloca
tal cual venga del GLB, calibrado a mano en Blender contra el ancho real del marcador impreso.
