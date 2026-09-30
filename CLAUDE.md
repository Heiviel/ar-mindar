# Instrucciones del proyecto

## Despliegue: dos repositorios (desde 2026-09-30)

Ademas de GitHub, la empresa ha dado un repo de Azure DevOps para alojar la
app (Vercel esta bloqueado por politica corporativa; Azure Static Web Apps
personal se descarto tambien a favor de la infraestructura de la empresa):

- **GitHub** (`origin`): `https://github.com/Heiviel/ar-mindar` — historial
  completo de desarrollo, con hook de push automatico (ver mas abajo).
- **Azure DevOps** (`azuredevops`): `https://dev.azure.com/DPD-DI/DPD_Webapp/_git/ARVision.Webapp.Frontend`
  — repo corporativo con pipeline propio (`devops/azure-pipeline.yaml`,
  extiende una plantilla compartida `DPD_Webapp/DPD.Webapp` con
  `appName: arvision`). **Historial deliberadamente separado y minimo**:
  no lleva todo el historial de iteraciones de GitHub, solo una foto del
  estado actual de los archivos imprescindibles (index.html +
  modelo/marcador de demo), para no ensuciar el repo del equipo. NUNCA
  fusionar el historial completo de `origin` con este remoto (historias no
  relacionadas — un merge/push directo se rechazaria o forzaria a un push
  destructivo).

**Como sincronizar cambios a Azure DevOps** (repetir cada vez que se quiera
publicar ahi, no es automatico):
```
git fetch azuredevops main
git branch -f corp-sync azuredevops/main
git checkout corp-sync
git checkout main -- index.html
git checkout main -- models/bola.glb targets/bola.mind targets/bola.jpeg
git add -A
git commit -m "Actualiza el visor AR"
git push azuredevops corp-sync:main
git checkout main
```
Ajustar la lista de `git checkout main -- <archivo>` a lo que realmente haya
cambiado — seguir siendo minimo (ver regla de abajo), no arrastrar
documentacion ni archivos de prueba a este repo salvo que el usuario lo pida.

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
