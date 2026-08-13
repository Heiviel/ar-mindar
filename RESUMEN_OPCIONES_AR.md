# Resumen rapido de opciones AR (MindAR + Three.js)

Fecha de actualizacion: 2026-08-12

## Contexto del proyecto
Proyecto WebAR sin build step basado en MindAR + Three.js.

Archivos principales:
- `index.html`: aplicacion AR principal.
- `test-camera.html`: prueba aislada de la camara.
- `models/bola.glb`: modelo 3D.
- `targets/bola.mind`: target compilado de MindAR.
- `targets/bola.jpeg`: imagen fisica del target.
- `ar-mindar.code-workspace`: workspace de VS Code.
- `index.backup-2026-08-11.html`: copia de seguridad anterior.

Dependencias actuales:
- MindAR 1.2.5.
- Three.js 0.160.0.

La version Three.js 0.162.0 se probo para soporte de `KHR_animation_pointer`,
pero se revirtio porque MindAR dejo de funcionar correctamente con esa combinacion.

## 1) Estados de objeto (prioridad alta)
Objetivo:
- Cambiar entre variantes del mismo modelo dentro de AR.

Formato acordado:
- Nombres con prefijo ST.
- Ejemplo: ST001, ST002.
- Ejemplo con etiqueta: ST001_excavacion, ST002_Pilotes.

Regla UI:
- ST001 -> se muestra 001.
- ST002 -> se muestra 002.
- ST001_excavacion -> se muestra excavacion.
- ST002_Pilotes -> se muestra Pilotes.
- Orden siempre por numero tras ST (001, 002, 003...).

Implementacion actual:
- Los estados se detectan automaticamente dentro de un unico GLB.
- No se usa JSON ni configuracion externa.
- Si hay dos o mas nodos ST, aparece el panel Estados.
- Solo un estado permanece visible cada vez.
- Al cambiar de estado se reconstruyen las referencias de mallas, capas,
  wireframe y seccion.

Reglas Blender:
- Cada estado debe ser un grupo u objeto raiz con nombre `ST...`.
- Todos los estados deben compartir origen, escala y orientacion.

## 2) Secciones (estado actual: aparcado)
Objetivo:
- Corte visual del modelo en tiempo real.

Lo probado:
- Cap solido tipo booleano en runtime.
- Banda de arista hacia interior.
- Malla cutter auxiliar.

Situacion real:
- Para resultados tipo Blender 100% estables en todos los casos,
  la topologia del modelo condiciona mucho.
- Se deja aparcado por ahora para no bloquear el flujo.

Implementacion que queda en el codigo:
- Plano de clipping en Planta, Alzado y Perfil.
- Slider de porcentaje de corte.
- Boton de inversion de lado en Alzado y Perfil.
- Banda fina de geometria hacia el interior de la pieza.
- Soporte opcional para malla auxiliar `__cutter`.

Limitacion conocida:
- Un recipiente abierto o una geometria que no define un volumen cerrado no puede
    generar de forma general un relleno solido identico al Boolean de Blender.
- Por eso la seccion no se considera cerrada ni fiable para todos los modelos.

## 3) Modos de visualizacion (implementado)
Modos:
- Sombreado
- Alambrico
- Sombreado + Alambrico

Extra:
- Selector de color para el alambrico.

Tambien existe:
- Selector de color por capa.
- En un material PBR texturizado, `material.color` multiplica la textura:
    blanco conserva la textura, otros colores la tiñen.

Problema pendiente:
- Hay que comprobar que todos los meshes del estado activo reciben el modo,
    incluso cuando algunas capas estan apagadas.
- El modo wireframe usa copias visuales de las geometrias.

## 4) Menu lateral tecnico (implementado)
Objetivo:
- Un panel lateral plegable para concentrar herramientas.

Incluye:
- Bloque Estados
- Bloque Visualizacion
- Bloque Iluminacion
- Bloque Animacion, solo si el GLB expone animaciones.
- Bloque Capas
- Bloque Seccion

Nota:
- Se pidio estilo pestaña discreta (no franja ancha permanente).

Controles actuales:
- Sombreado.
- Alambrico.
- Sombreado + Alambrico.
- Color del alambrico.
- Estabilidad de tracking.
- Intensidad de luz ambiente.
- Intensidad de luz direccional.
- Checkbox de sombras.
- Selector de color por capa.

## 5) Otras opciones que se valoraron
- Control de pose del modelo (escala/rotacion/offset + reset)
- Exploded view
- Hotspots interactivos
- Modo comparar (antes/despues)
- Calidad adaptativa por FPS
- Feedback de tracking
- Captura de imagen AR
- Multitarget
- Modo kiosco/demo

## 6) Opciones recomendadas (impacto vs esfuerzo)
1. Estados del objeto
- Alternar entre variantes del mismo modelo: vacio, equipado, montaje A/B, etc.
- Muy util para presentaciones tecnicas.
- Es estable y ligera en movil.

2. Control de pose del modelo en tiempo real
- Escala, rotacion y offset con UI simple.
- Boton de reset a valores de fabrica.
- Te evita reexportar GLB por ajustes finos.

3. Modo Exploded View
- Separar piezas por ejes con slider.
- Muy potente para ensenar ensamblajes.

4. Hotspots interactivos
- Puntos clicables con etiquetas y mini panel de informacion.
- Sirve para tours guiados de componentes.

5. Modo Comparar
- Antes/despues con un toggle o deslizador.
- Ideal para ensenar cambios entre estados.

6. Calidad adaptativa automatica
- Baja calidad cuando cae FPS y la recupera cuando sube.
- Mejora mucho la experiencia en moviles flojos.

7. Feedback de tracking
- Indicador visual de calidad del reconocimiento.
- Mensajes claros: busca mas luz, acercate, inclina marcador.
- Reduce frustracion del usuario final.

8. Captura para compartir
- Foto desde la camara AR con boton.
- Exporta imagen lista para enviar.

9. Soporte multitarget
- Un marcador para cada vista o familia de modelo.
- Escala bien para catalogos de piezas.

10. Modo kiosco / demo
- Inicio automatico, UI minima, bloqueo de opciones avanzadas.
- Perfecto para ensenar en reuniones o ferias.

## 7) Propuesta practica para empezar ya
1. Estados del objeto
2. Control de pose
3. Feedback de tracking

## 8) Capas tipo AutoCAD (implementado)
Las capas se detectan con prefijo `LY`.

Ejemplos:
- `LY001`
- `LY001_plancha_greca`
- `LY002_Hormigon`
- `LY003_ESTRUCTURA`

Reglas:
- `LY001` se muestra como `001`.
- `LY001_plancha_greca` se muestra como `plancha_greca`.
- El orden es numerico por el bloque tras `LY`.
- Varias capas pueden estar activas al mismo tiempo.
- El usuario puede apagar o encender cada capa.
- El selector de color de una capa modifica los materiales descendientes.

## 9) Iluminacion y sombras
Iluminacion actual:
- `HemisphereLight` para luz ambiente.
- `DirectionalLight` para luz direccional y sombras.
- Controles de intensidad desde el panel.

Plano de sombras:
- En Blender se puede añadir un plano llamado `__shadow`.
- Debe estar al nivel del marcador/suelo y cubrir el area deseada.
- El visor intenta aplicarle `ShadowMaterial`.
- Debe ser invisible salvo por la sombra.
- Debe quedar fuera de capas, secciones y wireframe.

Estado real:
- Se configuro `castShadow`, `receiveShadow` y el frustum de la luz.
- Hay que verificar en dispositivo si la sombra se proyecta correctamente.
- Una sombra sobre la superficie fisica real no es posible directamente sin
    una geometria receptora virtual alineada con el target.

## 10) Animaciones de material
Se intento exportar animacion de color con `KHR_animation_pointer`.

El visor tiene:
- `AnimationMixer`.
- Boton Play/Pausa.
- Slider de tiempo.
- Tiempo mostrado en segundos.

Pero el panel solo aparece si `gltf.animations.length > 0`.

Problema:
- El GLB exportado no esta entregando animaciones utilizables en `gltf.animations`.
- MindAR 1.2.5 funciona con Three.js 0.160.0.
- En la combinacion actual no se puede garantizar `KHR_animation_pointer`.

Decision practica pendiente:
- Cambiar el stack y buscar soporte real de `KHR_animation_pointer`.
- O abandonar animacion de material y usar materiales/variantes ciclabes.
- Los materiales ciclabes si son viables: pueden detectarse por nombre y cambiarse
    desde UI sin necesidad de animacion GLB.

## 11) Camaras y tracking
Observacion:
- La camara integrada del portatil es mas estable.
- La webcam Creative produce mas vibracion del modelo sobre el target.

El panel tiene un slider Estabilidad de 1 a 10:
- Bajo: mas reactivo y potencialmente mas vibracion.
- Alto: mas suavizado y potencialmente mas retardo.

Pendiente:
- Confirmar que el slider modifica realmente los filtros internos de MindAR.
- La API interna del filtro puede no estar expuesta de la forma que espera el codigo.

## 12) Estado exacto para continuar
Al probar un nuevo GLB, comprobar en este orden:
1. Se detectan `ST001`, `ST002`, etc.
2. Los botones muestran solo el sufijo correcto.
3. Se detectan `LY001`, `LY002`, etc.
4. Las capas se pueden apagar y encender.
5. Los tres modos de visualizacion afectan a todos los objetos.
6. Las texturas PBR cargan correctamente desde el GLB.
7. El selector de color tiñe la capa sin eliminar su textura.
8. El plano `__shadow` no aparece como geometria ni en wireframe.
9. El checkbox de sombras produce sombra sobre `__shadow`.
10. El panel Animacion aparece solo si el GLB expone animaciones validas.

## 13) Decisiones de arquitectura
- No usar JSON para estados o capas: el GLB contiene la estructura y el visor
    la detecta automaticamente.
- Estados `ST`: exclusivos, uno visible.
- Capas `LY`: independientes, varias visibles.
- `__shadow`: plano tecnico invisible para recibir sombras.
- `__cutter`: malla auxiliar opcional para secciones.
- Mantener siempre `DoubleSide` en la geometria visible.

## Recomendacion de siguiente paso
Para avanzar rapido y estable:
1. Dejar Estados fino y cerrado.
2. Consolidar UI lateral (usabilidad movil + desktop).
3. Luego retomar Secciones con una estrategia cerrada segun tipo de modelo.
