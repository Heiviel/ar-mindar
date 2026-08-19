# AR MindAR — Prototipo de reconocimiento de imagen

Web AR: enfoca un marcador impreso con la cámara del móvil y se lanza un modelo 3D encima.
Sin build step — HTML/JS puro, ES Modules vía CDN (three.js + MindAR + import map).

Este documento cubre instalación y deploy. Para el día a día (convenciones de nombres en el
GLB, uso del menú, etc.) ver [GUIA_USO.md](GUIA_USO.md).

## Estado actual — probado

`index.html` carga **assets de demo públicos de MindAR** (marcador "raccoon" + su modelo GLTF)
para que puedas probar el flujo completo en el móvil ANTES de meter tus propios archivos.
Verificado: las librerías cargan sin errores y MindAR arranca correctamente (three@0.160.0 +
GLTFLoader + mind-ar@1.2.5, todo como ES Modules vía import map — mind-ar 1.2.5 solo distribuye
build ESM, por eso NO se puede usar como `<script>` clásico).

Cuando lo pruebes con tu marcador y modelo, sustituyes 2 URLs (ver "Poner tus propios assets").

## Probar ya (deploy)

Necesitas HTTPS real para que el móvil dé permiso de cámara (excepto en `localhost`).

### Opción rápida: Vercel
```bash
npx vercel --prod
```
Sin configuración: detecta el `index.html` en la raíz y lo sirve tal cual.

### Opción rápida: Netlify
```bash
npx netlify-cli deploy --prod --dir .
```

### GitHub Pages
1. Sube el repo a GitHub.
2. Settings → Pages → Source: rama `main`, carpeta `/ (root)`.
3. Espera el deploy, abre la URL `https://<usuario>.github.io/<repo>/` en el móvil.

### Probar en local
`localhost` está exento del requisito HTTPS:
```bash
npx http-server -p 8080
```
Pero eso solo sirve para verificar que las librerías cargan sin errores desde tu propio PC.
Para probar con la cámara real del móvil necesitas HTTPS real → usa Vercel/Netlify/GH Pages.

## Poner tus propios assets

### 1. El marcador (imagen a enfocar)

Compila tu imagen (plano, dibujo, texto impreso) a `.mind` con el compilador oficial online:
https://hiukim.github.io/mind-ar-js-doc/tools/compile

- Sube tu imagen → descarga `targets.mind`.
- Cópialo a `targets/target.mind`.
- Imprime la MISMA imagen que compilaste (o muéstrala en pantalla) — eso es lo que la cámara reconoce.
- Buena imagen de marcador = mucho contraste y detalle (evita superficies lisas o patrones repetitivos).

### 2. El modelo 3D (GLB)

Exporta a `.glb` y cópialo a `models/model.glb`.

**Desde Blender:** File → Export → glTF 2.0 (.glb/.gltf) → formato `glTF Binary (.glb)`.

**Desde Rhino / CAD (STEP, IGES, OBJ, FBX):** Rhino no exporta GLB directamente. Pasa por Blender:
1. Importa el STEP/IGES en Rhino, exporta a OBJ o FBX (mejor: FBX si hay materiales/jerarquía).
2. Abre ese OBJ/FBX en Blender (File → Import).
3. Revisa escala (CAD suele venir en mm, three.js espera unidades "razonables" — ajusta `model.scale` en `index.html` si el modelo aparece gigante o invisible).
4. Export → glTF 2.0 → `.glb`.

### 3. Actualizar `index.html`

Cambia las dos URLs de demo por tus rutas locales:
```js
imageTargetSrc: './targets/target.mind',
```
```js
loader.load('./models/model.glb', ...)
```

## Estructura
```
index.html       ← toda la lógica AR (MindAR + three.js, ES Modules vía import map)
targets/          ← tu(s) marcador(es) compilado(s) (.mind)
models/           ← tus modelos GLB
```

## Notas técnicas

- **mind-ar@1.2.5 solo tiene build ESM** (usa `import` internamente) — por eso `index.html` usa
  `<script type="importmap">` + `<script type="module">`, no scripts clásicos. No cambies esto sin
  verificar que la nueva versión de mind-ar sigue siendo ESM.
- Un `.mind` puede contener varios marcadores (multi-imagen); `addAnchor(0)` usa el primero. Si compilas varios, usa `addAnchor(1)`, etc.
- El tracking de MindAR pierde precisión con marcadores pequeños o poca luz — pruébalo en condiciones reales antes de darlo por bueno.
- Si el modelo no aparece: mira la consola del navegador (o `chrome://inspect` desde PC conectando el móvil por USB) — casi siempre es escala (CAD en mm → modelo enorme/diminuto) o ruta 404.
