# Instrucciones del proyecto

## Backup a GitHub

Este repo tiene un hook `Stop` (`.claude/settings.json`) que hace commit + push
automatico a `origin/main` (https://github.com/Heiviel/ar-mindar) al terminar
cada sesion de Claude Code, si hay cambios pendientes.

**Regla: solo se sube el codigo necesario para que la app funcione.**
Nunca se suben `models/*` ni `targets/*` (excepto los `.gitkeep`) — el usuario
los selecciona en local desde la propia app (ver "Modelo y marcador" en
`GUIA_USO.md`), no hace falta tenerlos en el repo. Estan excluidos en
`.gitignore`. Si en algun momento hace falta versionar un asset puntual,
preguntar al usuario antes de sacarlo del `.gitignore` (los GLB grandes
ademas chocan con el limite de 100MB por archivo de GitHub).

**Excepcion permanente: `models/bola.glb`, `targets/bola.mind`, `targets/bola.jpeg`.**
Son el demo por defecto (`DEFAULT_TARGET_SRC`/`DEFAULT_MODEL_SRC` en
`index.html`) y SI se versionan — son fijos y pequenos, no contenido de
trabajo en curso. Sin ellos la app rompe nada mas desplegarse (peticion a
un 404, MindAR falla al decodificar la respuesta como si fuera el `.mind`
real: "RangeError: Extra N of M byte(s) found..."). No los saques del
repo aunque la regla de arriba diga "nunca modelos/targets".

**Excepciones puntuales: solo si el usuario lo pide explicitamente en ese
momento** (ej. "sube el modelo X para poder probar Y"). No es una relajacion
general de la regla — cada vez que el usuario no diga nada, la regla de
arriba (nunca modelos/targets de trabajo) sigue aplicando tal cual. No
asumir que un archivo puntual subido antes (como `models/personas.glb`,
subido 2026-09-03 para probar el cambio de modelo en caliente del modo
Suelo) sienta precedente para subir otros sin pedirlo cada vez.
